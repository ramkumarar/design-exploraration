# Google Vertex AI Gemini 2.5 Pro - Curl Examples

## Prerequisites
- Replace `YOUR_PROJECT_ID` with your actual Google Cloud project ID
- Replace `YOUR_ACCESS_TOKEN` with a valid OAuth 2.0 access token
- Choose your preferred region (examples use `us-central1`)

## 1. Content Generation (Non-streaming)

### Request
```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "role": "user",
      "parts": [{
        "text": "Write a short poem about artificial intelligence."
      }]
    }],
    "generationConfig": {
      "temperature": 0.7,
      "maxOutputTokens": 256,
      "topP": 0.8,
      "topK": 40
    }
  }' \
  "https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID/locations/us-central1/publishers/google/models/gemini-2.5-pro:generateContent"
```

### Response
```json
{
  "candidates": [{
    "content": {
      "parts": [{
        "text": "In circuits bright and logic deep,\nWhere algorithms wake from digital sleep,\nA mind of silicon dreams and learns,\nAs through vast networks knowledge burns.\n\nNo flesh, no bone, yet thoughts arise,\nFrom code that mimics human eyes,\nAI whispers in electric streams,\nBuilding bridges between realities and dreams."
      }]
    },
    "finishReason": "STOP",
    "safetyRatings": [{
      "category": "HARM_CATEGORY_SEXUALLY_EXPLICIT",
      "probability": "NEGLIGIBLE",
      "blocked": false
    }]
  }],
  "usageMetadata": {
    "promptTokenCount": 12,
    "candidatesTokenCount": 67,
    "totalTokenCount": 79
  },
  "modelVersion": "gemini-2.5-pro-001"
}
```

## 2. Streaming Content Generation

### Request
```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [{
      "role": "user",
      "parts": [{
        "text": "Explain quantum computing in simple terms."
      }]
    }],
    "generationConfig": {
      "temperature": 0.5,
      "maxOutputTokens": 200
    }
  }' \
  "https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID/locations/us-central1/publishers/google/models/gemini-2.5-pro:streamGenerateContent"
```

### Response (Streaming)
```json
data: {"candidates": [{"content": {"parts": [{"text": "Quantum"}]}, "finishReason": "STOP", "safetyRatings": []}], "usageMetadata": {"promptTokenCount": 8}}

data: {"candidates": [{"content": {"parts": [{"text": " computing is"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " like having a"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " super-powerful calculator that"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " can explore many"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " possible answers"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " simultaneously. Unlike regular"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " computers that process information"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " in bits (0s and 1s),"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " quantum computers use"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " quantum bits (qubits) that"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " can be 0, 1, or"}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": " both at the same time."}]}, "finishReason": "STOP", "safetyRatings": []}]}

data: {"candidates": [{"content": {"parts": [{"text": ""}]}, "finishReason": "STOP", "safetyRatings": [], "citationMetadata": {}, "avgLogprobs": -0.1}], "usageMetadata": {"promptTokenCount": 8, "candidatesTokenCount": 45, "totalTokenCount": 53}, "modelVersion": "gemini-2.5-pro-001"}
```

## 3. Chat/Multi-turn Conversation

### Request
```bash
curl -X POST \
  -H "Authorization: Bearer YOUR_ACCESS_TOKEN" \
  -H "Content-Type: application/json" \
  -d '{
    "contents": [
      {
        "role": "user",
        "parts": [{
          "text": "Hello! Can you help me with Python programming?"
        }]
      },
      {
        "role": "model",
        "parts": [{
          "text": "Hello! I'd be happy to help you with Python programming. What specific topic or problem would you like assistance with?"
        }]
      },
      {
        "role": "user",
        "parts": [{
          "text": "How do I create a simple function to calculate the area of a circle?"
        }]
      }
    ],
    "generationConfig": {
      "temperature": 0.3,
      "maxOutputTokens": 150
    }
  }' \
  "https://us-central1-aiplatform.googleapis.com/v1/projects/YOUR_PROJECT_ID/locations/us-central1/publishers/google/models/gemini-2.5-pro:generateContent"
```

### Response
```json
{
  "candidates": [{
    "content": {
      "parts": [{
        "text": "Here's a simple Python function to calculate the area of a circle:\n\n```python\nimport math\n\ndef circle_area(radius):\n    \"\"\"\n    Calculate the area of a circle given its radius.\n    \n    Args:\n        radius (float): The radius of the circle\n    \n    Returns:\n        float: The area of the circle\n    \"\"\"\n    return math.pi * radius ** 2\n\n# Example usage:\nradius = 5\narea = circle_area(radius)\nprint(f\"The area of a circle with radius {radius} is {area:.2f}\")\n```\n\nThis function uses the formula: Area = π × r²"
      }]
    },
    "finishReason": "STOP",
    "safetyRatings": [{
      "category": "HARM_CATEGORY_SEXUALLY_EXPLICIT",
      "probability": "NEGLIGIBLE",
      "blocked": false
    }, {
      "category": "HARM_CATEGORY_HATE_SPEECH",
      "probability": "NEGLIGIBLE",
      "blocked": false
    }, {
      "category": "HARM_CATEGORY_HARASSMENT",
      "probability": "NEGLIGIBLE",
      "blocked": false
    }, {
      "category": "HARM_CATEGORY_DANGEROUS_CONTENT",
      "probability": "NEGLIGIBLE",
      "blocked": false
    }]
  }],
  "usageMetadata": {
    "promptTokenCount": 47,
    "candidatesTokenCount": 142,
    "totalTokenCount": 189
  },
  "modelVersion": "gemini-2.5-pro-001"
}
```

## Getting Your Access Token

To get your OAuth 2.0 access token, you can use:

```bash
# Using gcloud CLI (recommended)
gcloud auth print-access-token

# Or using service account key
gcloud auth activate-service-account --key-file=path/to/service-account.json
gcloud auth print-access-token
```

## Notes

1. **Authentication**: All requests require a valid OAuth 2.0 Bearer token in the Authorization header
2. **Streaming**: The streaming endpoint returns Server-Sent Events (SSE) format with `data:` prefixed JSON chunks
3. **Chat Context**: For multi-turn conversations, include the entire conversation history in the `contents` array
4. **Rate Limits**: Be aware of API quotas and rate limits for your project
5. **Regions**: Choose the region closest to your users for better latency
6. **Model Versions**: You can use `gemini-2.5-pro` for auto-updated versions or `gemini-2.5-pro-001` for specific versions