```markdown
# LLD: Secure CI/CD with GitLab Runner, Kubernetes, and Rootless Podman

## 1. Problem Statement

I want to use GitLab Runner with the Kubernetes executor for CI/CD pipelines. A critical requirement is the ability to run
integration tests that depend on external services like databases (PostgreSQL, Redis) and object storage (Minio). The standard approach for this has been Testcontainers, a library that programmatically spins up Docker containers.

The previous solution using Docker-in-Docker (DinD) is not secure and scalable and will not pass through security assessment due to its inherent risks, primarily the requirement for `privileged` containers, which effectively grants root access to the underlying Kubernetes node.

We need a replacement architecture that:
*   Enables the use of Testcontainers within CI jobs.
*   Eliminates the need for privileged containers.
*   Runs all processes as non-root users.
*   Provides efficient caching for both build dependencies (Maven/Gradle) and container images.
*   Maintains a strong security posture in a multi-tenant Kubernetes cluster.

## 2. Proposed Solution Overview

The proposed solution is to leverage **rootless Podman** running as a sidecar "service" container within the GitLab Runner job pod. The build container, containing the Testcontainers library, will communicate with the Podman service via a shared Unix socket. All containers (build, service, and test containers) run within a single, unprivileged Kubernetes pod, sharing a network namespace and volumes.

This design achieves all goals by replacing the high-risk privileged Docker daemon with a secure, rootless Podman daemon scoped entirely to the lifecycle and permissions of a single CI job pod.

## 3. Low-Level Architecture

### 3.1. Core Components

*   **GitLab Runner Pod:** A single, unprivileged Kubernetes Pod created by the GitLab Runner for each CI job. It acts as the security and resource boundary for all job-related processes.
*   **Build Container:** The main container where the user's script executes (e.g., `mvn clean verify`). It runs a non-root user and contains the application code, build tools, and the Testcontainers library.
*   **Podman Service Container:** A sidecar container running in the same pod. It exposes a Docker-compatible API over a Unix socket by running the `podman system service` command. It operates in rootless mode.
*   **Test Containers:** Ephemeral containers (e.g., PostgreSQL, Kafka) created by the Podman service upon request from Testcontainers. They run *inside* the GitLab Runner Pod's namespaces, not as separate pods.
*   **Shared Volumes:**
    *   **Socket Volume (`emptyDir`):** A memory-backed `emptyDir` volume shared between the build and Podman containers to host the `podman.sock` file for fast, low-latency communication.
    *   **Cache Volumes (`PVC`):** Kubernetes Persistent Volume Claims mounted into the pod to provide a persistent, shared filesystem for caching Maven/Gradle dependencies and Podman container images.

### 3.2. Architectural Diagram

```text
┌──────────────────────────────────────────────────────────────────────────────────┐
│                             KUBERNETES CLUSTER                                   │
│                                                                                  │
│  ┌────────────────────────────────────────────────────────────────────────────┐  │
│  │                          GITLAB RUNNER JOB POD                             │  │
│  │                   (Single, Unprivileged, Non-root Pod)                     │  │
│  │                                                                            │  │
│  │  ┌──────────────────────────┐      ┌───────────────────────────────────┐   │  │
│  │  │     BUILD CONTAINER      │      │       PODMAN SERVICE CONTAINER      │   │  │
│  │  │ (Image: maven:3-openjdk-17)│      │     (Image: quay.io/podman/stable)    │   │  │
│  │  │ User: non-root (e.g. 1001) │      │     User: non-root (e.g. 1001)      │   │  │
│  │  │──────────────────────────│      │───────────────────────────────────│   │  │
│  │  │ - mvn clean verify       │      │ - podman system service --root ...  │   │  │
│  │  │ - Testcontainers library │      │ - Listens on /sockets/podman.sock   │   │  │
│  │  │                          │      │                                   │   │  │
│  │  │ DOCKER_HOST=             │      │ ┌───────────────────────────────┐ │   │  │
│  │  │ /sockets/podman.sock     │◄─────┼─► Creates containers on request │ │   │  │
│  │  └───────────┬──────────────┘      │ └───────────────────────────────┘ │   │  │
│  │              │                     │        │                          │   │  │
│  │              │(localhost:5432)     │        ▼                          │   │  │
│  │              │                     │  ┌───────────────────────────┐    │   │  │
│  │              └────────────────────►│  │ Containers created by Podman  │    │   │  │
│  │                                    │  │ (Inside this Pod's Net NS)  │    │   │  │
│  │                                    │  │  - Postgres Container       │    │   │  │
│  │                                    │  └───────────────────────────┘    │   │  │
│  └────────────────────────────────────┴────────────────────────────────────┘   │  │
│                          ▲                          ▲                          │
│                          │ (Volume Mounts)          │ (Volume Mounts)          │
│  ┌───────────────────────┴──────────────────────────┴───────────────────────┐  │
│  │                               SHARED VOLUMES                             │  │
│  │ ┌────────────────────────┐  ┌──────────────────┐  ┌────────────────────┐ │  │
│  │ │ emptyDir (in-memory)   │  │ PVC: build-cache │  │ PVC: podman-cache  │ │  │
│  │ │ Mount: /sockets        │  │ Mount: /cache    │  │ Mount: /podman-data│ │  │
│  │ │ ---------------------- │  │ ---------------- │  │ ------------------ │ │  │
│  │ │ 📄 podman.sock         │  │ 📁 .m2/          │  │ 📁 storage/        │ │  │
│  │ └────────────────────────┘  └──────────────────┘  └────────────────────┘ │  │
│  └──────────────────────────────────────────────────────────────────────────┘  │
│                                                                                  │
└──────────────────────────────────────────────────────────────────────────────────┘
```

## 4. The Role of the Unix Socket (`podman.sock`)

The Unix socket is the cornerstone of communication in this architecture. It acts as the secure and efficient bridge between the build environment and the container engine.

### 4.1. What is it?
A Unix Domain Socket is an Inter-Process Communication (IPC) mechanism that allows processes on the same machine to exchange data. Unlike a network socket, it does not use the network stack (IP/TCP); instead, it appears as a special file on the filesystem (e.g., `/sockets/podman.sock`).

In our design, the `podman system service` command creates and listens on this socket, exposing a REST API that is **compatible with the Docker Engine API**.

### 4.2. Why is it necessary?
Libraries like **Testcontainers are hard-coded to communicate using the Docker Engine API**. They do not speak a "Podman native" protocol. The `podman system service` acts as a translation layer:
1.  **Testcontainers** sends a standard Docker API request (e.g., `POST /containers/create`) to the socket.
2.  The **Podman Service** receives this request, understands it, and translates it into an equivalent `podman` command (e.g., `podman run ...`).
3.  Podman executes the command and sends a Docker API-compliant response back to Testcontainers.

This compatibility layer allows us to adopt Podman for its security benefits without needing to change or fork popular tools like Testcontainers.

### 4.3. Lifecycle and Hosting on `emptyDir`
The socket is ephemeral and only needs to exist for the duration of a single CI job. Using a Kubernetes `emptyDir` volume is the perfect choice for this:
*   **Performance:** We configure the `emptyDir` with `medium: Memory`, which creates a `tmpfs` (in-memory) filesystem. Communication over the socket is extremely fast, as it avoids disk I/O and network latency. This is critical for the rapid creation and destruction of test containers.
*   **Isolation:** The `emptyDir` volume is created when the pod starts and is completely destroyed when the pod terminates. The socket is never exposed outside the pod, preventing any other workload on the cluster from accessing it.
*   **Simplicity:** No persistent state is needed for the socket, so `emptyDir` avoids the overhead and complexity of using a PVC.

The communication flow is as follows:
1.  The `podman-service` container starts and creates the `/sockets/podman.sock` file.
2.  The `build` container, via Testcontainers, connects to this same file at `/sockets/podman.sock`.
3.  Since both containers have the `/sockets` volume mounted, they are accessing the exact same in-memory file, enabling seamless communication.

## 5. Implementation Details

### 5.1. Kubernetes Prerequisites (PVCs)

You must create `PersistentVolumeClaim` resources in your Kubernetes cluster for caching.

**Example `pvc.yaml`:**
```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-build-cache-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 20Gi
  storageClassName: "standard" # Replace with your StorageClass
---
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: gitlab-podman-cache-pvc
spec:
  accessModes:
    - ReadWriteOnce
  resources:
    requests:
      storage: 50Gi # Container images can be large
  storageClassName: "standard" # Replace with your StorageClass
```

### 5.2. GitLab Runner Configuration (`config.toml`)

This configuration is the key to enabling the architecture.

```toml
# In /etc/gitlab-runner/config.toml

[[runners]]
  name = "kubernetes-podman-runner"
  executor = "kubernetes"
  [runners.kubernetes]
    image = "ubuntu:22.04"
    privileged = false

    # SECURITY: Enforce all containers in the pod to run as a specific non-root user and group.
    # `fs_group` automatically sets permissions on volumes, removing the need for `chown` in scripts.
    [runners.kubernetes.security_context]
      run_as_user = 1001
      run_as_group = 1001
      fs_group = 1001

    # Volume for the Unix socket (fast, in-memory, ephemeral)
    [[runners.kubernetes.volumes.empty_dir]]
      name = "podman-socket"
      mount_path = "/sockets"
      medium = "Memory"

    # Volume for persistent build tool caches
    [[runners.kubernetes.volumes.pvc]]
      name = "gitlab-build-cache-pvc"
      mount_path = "/cache"

    # Volume for persistent Podman image caches
    [[runners.kubernetes.volumes.pvc]]
      name = "gitlab-podman-cache-pvc"
      mount_path = "/podman-data"

    # Define the Podman sidecar service
    [[runners.kubernetes.services]]
      name = "quay.io/podman/stable"
      alias = "podman"
      command = [
        "podman",
        "--storage-driver=vfs",
        "--root=/podman-data/storage",
        "system",
        "service",
        "--time=0",
        "unix:///sockets/podman.sock"
      ]
```

### 5.3. GitLab Pipeline Configuration (`.gitlab-ci.yml`)

This is an example of how a developer would configure their pipeline.

```yaml
# .gitlab-ci.yml

variables:
  # Point build tools to the persistent cache volume.
  MAVEN_OPTS: "-Dmaven.repo.local=/cache/.m2/repository"
  GRADLE_USER_HOME: "/cache"

  # Tell Testcontainers where the Docker-compatible socket is.
  DOCKER_HOST: "unix:///sockets/podman.sock"
  # CRITICAL: Disable Ryuk. Job cleanup is handled by GitLab/Kubernetes.
  TESTCONTAINERS_RYUK_DISABLED: "true"

integration-test:
  stage: test
  image: maven:3.8-openjdk-17
  tags:
    - kubernetes-podman-runner

  script:
    - echo "Starting Maven build and test..."
    - mvn clean verify

# The 'cache:' key is no longer needed, as caching is handled by the more
# performant and reliable PVCs configured in the runner.
```

## 6. Security Considerations and Benefits

*   **No Privileged Containers:** The entire design operates without the `privileged: true` flag, drastically reducing the attack surface and preventing container breakout to the host node.
*   **Rootless Execution:** All processes in all containers (build, podman, and test containers) run as a non-root user defined in the runner's `security_context`. The UID inside the container is an unprivileged high-number UID on the Kubernetes node.
*   **Pod-Scoped Isolation:** The Podman daemon and its socket are completely isolated within the job pod. They cannot be accessed by other pods and are destroyed when the job finishes. This is a fundamental improvement over a shared, system-wide Docker daemon.
*   **Defense in Depth:** This model layers multiple security controls: Kubernetes Network Policies, Pod Security Standards, non-root containers, and the inherent security of rootless Podman with user namespaces.

## 7. Summary of Key Decisions

*   **Podman over Docker-in-Docker:** Chosen for its rootless, daemonless architecture that eliminates the need for privileged containers.
*   **`podman system service`:** Used to provide a Docker-compatible API, ensuring seamless integration with Testcontainers and other standard tooling.
*   **`emptyDir` for Socket:** Chosen for performance and its ephemeral lifecycle, which perfectly matches the needs of an IPC socket.
*   **PVCs for Caches:** Used for build tool and container image caching to ensure persistence and high performance across multiple jobs.
*   **`vfs` Storage Driver:** Selected for the Podman service as it is the most stable and compatible driver for running inside an unprivileged container without access to kernel overlay filesystems.
*   **Disable Ryuk (`TESTCONTAINERS_RYUK_DISABLED`):** Ryuk is the resource reaper for Testcontainers. It is unnecessary and problematic in this environment, as Kubernetes is responsible for cleaning up the entire pod and all its resources.
```
