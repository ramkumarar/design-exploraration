# Requirements Template for Roo Jira Planner

## Project Overview

### Project Name
[Clear, descriptive project name]

### Project Type
- [ ] New Feature Development
- [ ] Enhancement to Existing Feature
- [ ] Bug Fix Initiative
- [ ] Technical Debt Cleanup
- [ ] Infrastructure Improvement
- [ ] Data/Analytics Project

### Project Classification
- **Complexity Level**: [Simple (1-2 sprints) | Moderate (3-6 sprints) | Complex (6+ sprints)]
- **Planning Urgency**: [Comprehensive | Standard | Rapid]
- **Risk Level**: [Low | Medium | High]

### Executive Summary
[2-3 sentences describing what this project delivers and why it matters to the business]

### Business Problem Statement
[Clear description of the problem being solved, including current pain points and their impact]

### Success Vision
[Description of what success looks like when this project is complete]

## Stakeholder Analysis

### Primary Stakeholders
| Stakeholder | Role | Primary Needs | Success Criteria |
|-------------|------|---------------|------------------|
| [Name/Role] | [Title] | [What they need] | [How they measure success] |
| [Name/Role] | [Title] | [What they need] | [How they measure success] |

### User Personas
#### Persona 1: [Persona Name]
- **Role**: [Job title/function]
- **Goals**: [What they're trying to achieve]
- **Pain Points**: [Current frustrations/challenges]
- **Technical Proficiency**: [Beginner/Intermediate/Advanced]
- **Usage Frequency**: [Daily/Weekly/Monthly/Occasional]
- **Key Workflows**: [Primary tasks they perform]

#### Persona 2: [Persona Name]
- **Role**: [Job title/function]
- **Goals**: [What they're trying to achieve]
- **Pain Points**: [Current frustrations/challenges]
- **Technical Proficiency**: [Beginner/Intermediate/Advanced]
- **Usage Frequency**: [Daily/Weekly/Monthly/Occasional]
- **Key Workflows**: [Primary tasks they perform]

## Business Context

### Business Value Proposition
- **Primary Value**: [Main business benefit]
- **Secondary Values**: [Additional benefits]
- **Quantified Impact**: [Specific metrics, cost savings, revenue impact]
- **Strategic Alignment**: [How this supports company goals]

### Success Metrics & KPIs
| Metric | Current State | Target State | Measurement Method |
|--------|---------------|--------------|-------------------|
| [Metric 1] | [Current value] | [Target value] | [How measured] |
| [Metric 2] | [Current value] | [Target value] | [How measured] |
| [Metric 3] | [Current value] | [Target value] | [How measured] |

### Business Constraints
- **Budget**: [Budget limitations or considerations]
- **Timeline**: [Hard deadlines or time constraints]
- **Resources**: [Team size, skill constraints]
- **Compliance**: [Regulatory or policy requirements]
- **Business Rules**: [Specific business logic that must be followed]

## Functional Requirements

### Core Features
#### Feature 1: [Feature Name]
**Business Justification**: [Why this feature is needed]
**User Story**: As a [persona], I want [capability], so that [business outcome]
**Priority**: [Critical/High/Medium/Low]
**Complexity**: [Simple/Moderate/Complex]

**Detailed Requirements**:
1. [Specific requirement 1]
2. [Specific requirement 2]
3. [Specific requirement 3]

**Acceptance Criteria** (EARS Format):
1. GIVEN [precondition] WHEN [action] THEN [expected result]
2. GIVEN [precondition] WHEN [action] THEN [expected result]
3. GIVEN [precondition] WHEN [action] THEN [expected result]

**Business Rules**:
- [Rule 1]
- [Rule 2]

#### Feature 2: [Feature Name]
**Business Justification**: [Why this feature is needed]
**User Story**: As a [persona], I want [capability], so that [business outcome]
**Priority**: [Critical/High/Medium/Low]
**Complexity**: [Simple/Moderate/Complex]

**Detailed Requirements**:
1. [Specific requirement 1]
2. [Specific requirement 2]
3. [Specific requirement 3]

**Acceptance Criteria** (EARS Format):
1. GIVEN [precondition] WHEN [action] THEN [expected result]
2. GIVEN [precondition] WHEN [action] THEN [expected result]
3. GIVEN [precondition] WHEN [action] THEN [expected result]

**Business Rules**:
- [Rule 1]
- [Rule 2]

### Supporting Features
#### Feature 3: [Feature Name]
**Business Justification**: [Why this feature is needed]
**User Story**: As a [persona], I want [capability], so that [business outcome]
**Priority**: [Critical/High/Medium/Low]
**Complexity**: [Simple/Moderate/Complex]

**Detailed Requirements**:
1. [Specific requirement 1]
2. [Specific requirement 2]

**Acceptance Criteria** (EARS Format):
1. GIVEN [precondition] WHEN [action] THEN [expected result]
2. GIVEN [precondition] WHEN [action] THEN [expected result]

## Non-Functional Requirements

### Performance Requirements
- **Response Time**: [Maximum acceptable response times]
- **Throughput**: [Expected transaction volume]
- **Concurrent Users**: [Number of simultaneous users]
- **Scalability**: [Growth expectations and scaling requirements]

### Security Requirements
- **Authentication**: [How users will be authenticated]
- **Authorization**: [Access control requirements]
- **Data Protection**: [Encryption, PII handling requirements]
- **Compliance**: [Security standards that must be met]

### Reliability & Availability
- **Uptime**: [Required availability percentage]
- **Recovery Time**: [Maximum acceptable downtime]
- **Backup Requirements**: [Data backup and recovery needs]
- **Error Handling**: [How errors should be managed]

### Usability Requirements
- **User Experience**: 
  - Intuitive navigation and user flows
  - Clear error messages and user feedback
  - Consistent design and branding
  - Form validation and user guidance
- **Accessibility**: WCAG 2.1 AA compliance for users with disabilities
- **Browser Support**: [Supported browsers based on user base]
- **Mobile Support**: [Mobile/tablet support requirements based on user needs]
- **Performance**: 
  - Page load times that don't impact user productivity
  - System responsiveness during peak usage
  - Acceptable response times for user interactions

### Integration Requirements
- **External Systems**: [Business systems that need to be integrated with]
- **Third-Party Services**: [External services dependencies like payment gateways, email services, etc.]
- **Data Exchange**: [What information needs to be shared between systems]
- **Real-time vs Batch**: [Business requirements for data synchronization timing]

## Technical Context

### Current Technology Stack
- **Frontend**: React 18+ (Single Page Application or Micro Frontend)
  - State Management: [Redux/Zustand/Context API]
  - UI Framework: [Material-UI/Ant Design/Tailwind CSS]
  - Build Tool: [Vite/Create React App/Webpack]
  - Testing: Jest, Cypress, Playwright
- **Backend**: Java 17 + Spring Boot 3.x Microservices
  - Framework: Spring Boot, Spring Security, Spring Data JPA
  - API: RESTful APIs with OpenAPI/Swagger documentation
  - Testing: JUnit, Pactflow (contract testing), Spring slices (integration testing), Karate (acceptance testing)
  - Message Queue: [RabbitMQ/Apache Kafka/AWS SQS] (if applicable)
  - Caching: [Redis/Hazelcast] (if applicable)
- **Database**: PostgreSQL 14+
  - Connection Pooling: HikariCP
  - Migration: Flyway/Liquibase
  - Monitoring: [pg_stat_statements/pgAdmin]
- **Infrastructure**: [Docker/Kubernetes/AWS/Azure/GCP]
- **Third-Party Tools**: [Current integrations and tools]

### Technical Constraints
- **Frontend Constraints**:
  - Must be compatible with React 18+ and modern browsers (Chrome 90+, Firefox 88+, Safari 14+)
  - Single Page Application routing with React Router
  - Responsive design for desktop and mobile (if applicable)
  - Accessibility compliance (WCAG 2.1 AA)
- **Backend Constraints**:
  - Java 17 LTS with Spring Boot 3.x framework
  - RESTful API design following OpenAPI 3.0 specification
  - Microservice architecture with service discovery
  - Database transactions must maintain ACID properties
- **Database Constraints**:
  - PostgreSQL 14+ with proper indexing strategies
  - Database migrations must be backward compatible
  - Connection pooling with maximum [X] connections per service
- **Integration Constraints**: [API rate limits, data format requirements, authentication protocols]

### Team Capabilities
- **Team Size**: [Number of developers, designers, etc.]
- **Skill Levels**: [Junior/Mid/Senior distribution]
- **Technology Expertise**:
  - **React/Frontend**: [Team's React experience level - beginner/intermediate/expert]
  - **Java/Spring Boot**: [Team's Spring Boot experience level - beginner/intermediate/expert]
  - **PostgreSQL**: [Team's database design and optimization experience]
  - **Microservices**: [Team's experience with distributed systems]
  - **DevOps**: [Team's experience with CI/CD, containerization]
- **Domain Knowledge**: [Team's familiarity with business domain]

## Dependencies & Assumptions

### External Dependencies
| Dependency | Type | Impact | Risk Level | Mitigation |
|------------|------|--------|------------|------------|
| [Dependency 1] | [Technical/Business/Legal] | [High/Medium/Low] | [High/Medium/Low] | [Mitigation strategy] |
| [Dependency 2] | [Technical/Business/Legal] | [High/Medium/Low] | [High/Medium/Low] | [Mitigation strategy] |

### Assumptions
1. [Assumption 1 - with validation plan]
2. [Assumption 2 - with validation plan]
3. [Assumption 3 - with validation plan]

### Known Risks
| Risk | Probability | Impact | Mitigation Strategy |
|------|-------------|--------|-------------------|
| [Risk 1] | [High/Medium/Low] | [High/Medium/Low] | [How to mitigate] |
| [Risk 2] | [High/Medium/Low] | [High/Medium/Low] | [How to mitigate] |

## Scope Definition

### In Scope
- [Clearly defined item 1]
- [Clearly defined item 2]
- [Clearly defined item 3]

### Out of Scope
- [Explicitly excluded item 1]
- [Explicitly excluded item 2]
- [Explicitly excluded item 3]

### Future Considerations
- [Items for future phases/releases]
- [Nice-to-have features not in current scope]

## User Journeys & Workflows

### Primary User Journey 1: [Journey Name]
**Persona**: [Which persona]
**Trigger**: [What starts this journey]
**Goal**: [What the user wants to achieve]

**Steps**:
1. [Step 1 - with expected outcome]
2. [Step 2 - with expected outcome]
3. [Step 3 - with expected outcome]
4. [Step 4 - with expected outcome]

**Success Criteria**: [How we know the journey was successful]
**Pain Points**: [Current friction in this journey]

### Primary User Journey 2: [Journey Name]
**Persona**: [Which persona]
**Trigger**: [What starts this journey]
**Goal**: [What the user wants to achieve]

**Steps**:
1. [Step 1 - with expected outcome]
2. [Step 2 - with expected outcome]
3. [Step 3 - with expected outcome]

**Success Criteria**: [How we know the journey was successful]
**Pain Points**: [Current friction in this journey]

## Business Data Requirements

### Data Concepts (Business Level)
#### Business Entity 1: [Business Concept Name]
- **Business Purpose**: [Why this information is needed from business perspective]
- **Business Owner**: [Who owns this data in the organization]
- **Data Sources**: [Where this information comes from - external systems, user input, etc.]
- **Business Rules**: [How this data behaves from business perspective]
- **Compliance Requirements**: [Any regulatory or policy requirements for this data]

#### Business Entity 2: [Business Concept Name]
- **Business Purpose**: [Why this information is needed from business perspective]
- **Business Owner**: [Who owns this data in the organization]
- **Data Sources**: [Where this information comes from - external systems, user input, etc.]
- **Business Rules**: [How this data behaves from business perspective]
- **Compliance Requirements**: [Any regulatory or policy requirements for this data]

### Data Volume & Growth
- **Expected Data Volume**: [Business estimates of data size]
- **Growth Projections**: [How data is expected to grow over time]
- **Retention Requirements**: [How long data needs to be kept for business/legal reasons]

### Data Quality Requirements
- **Accuracy Requirements**: [Business expectations for data accuracy]
- **Completeness Requirements**: [What data fields are mandatory vs optional]
- **Timeliness Requirements**: [How fresh the data needs to be]

## Validation & Testing Strategy

### Acceptance Testing Approach
- **User Acceptance Testing**: [Who will perform UAT and criteria]
- **Business Validation**: [How business rules will be validated]
- **Performance Testing**: [Performance validation approach]
- **Security Testing**: [Security validation requirements]

### Test Data Requirements
- **Test Scenarios**: [Key scenarios that must be tested]
- **Test Data Volume**: [Amount of test data needed]
- **Test Data Sources**: [Where test data will come from]

## Questions & Clarifications Needed

### Open Questions
1. [Question 1 - with context of why it matters]
2. [Question 2 - with context of why it matters]
3. [Question 3 - with context of why it matters]

### Areas Needing Clarification
1. [Area 1 - with specific questions]
2. [Area 2 - with specific questions]

### Decisions Pending
1. [Decision 1 - with options and implications]
2. [Decision 2 - with options and implications]

---

## Template Usage Instructions

### For Project Managers/Product Owners:
1. Fill out all sections as completely as possible
2. Focus especially on business context, user personas, and success metrics
3. Be specific about constraints and dependencies
4. Include quantified business value where possible

### For Business Analysts:
1. Ensure all functional requirements have clear acceptance criteria
2. Map requirements to specific user personas
3. Validate that user journeys are complete and realistic
4. Include edge cases and error scenarios

### For Technical Leads:
1. Provide detailed technical context and constraints
2. Identify integration points and dependencies
3. Assess team capabilities honestly
4. Flag technical risks and unknowns

### For Roo Planner Integration:
This template provides all the context needed for the Roo planner to:
- Classify the project appropriately
- Create strategic epics aligned with business value
- Generate detailed user stories with proper acceptance criteria
- Break down tasks with appropriate technical detail
- Estimate effort using the smart estimation framework
- Identify dependencies and risks accurately

The more complete and specific the requirements, the better the Roo planner can create actionable Jira stories and tasks.

## Template Completion Guidelines

### For Business Stakeholders:
- Focus on business value, user needs, and success metrics
- Provide specific, measurable outcomes where possible
- Include realistic timelines and budget constraints

### For Product Owners:
- Ensure user stories reflect real user needs
- Prioritize features based on business value
- Define clear acceptance criteria for each requirement

### For Technical Teams:
- Provide honest assessment of technical constraints
- Identify integration points and dependencies
- Flag areas of technical uncertainty or risk