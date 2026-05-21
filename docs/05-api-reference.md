# API Reference

> This document defines the REST API for interacting with Heya Factory.

---

## 1. API Overview

### Base URL

```
Production: https://api.heya.io/v1
Local:      http://localhost:8080/v1
```

### Content Types

```
Content-Type: application/json
Accept: application/json
```

### Authentication

All API requests require authentication via Bearer token:

```http
Authorization: Bearer <token>
```

Tokens can be obtained via:
- API keys (for service accounts)
- OAuth 2.0 (for user accounts)
- Session tokens (for web UI)

---

## 2. Factory Management

### List Factories

```http
GET /factories
```

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `limit` | integer | Max results (default: 20) |
| `offset` | integer | Pagination offset |
| `tag` | string | Filter by tag |
| `owner` | string | Filter by owner |

**Response:**

```json
{
  "factories": [
    {
      "name": "game-engine-factory",
      "version": "1.0.0",
      "displayName": "Game Engine Factory",
      "status": "active",
      "createdAt": "2026-05-01T00:00:00Z"
    }
  ],
  "total": 1,
  "limit": 20,
  "offset": 0
}
```

---

### Get Factory

```http
GET /factories/{name}
```

**Response:**

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "FactoryBlueprint",
  "metadata": {
    "name": "game-engine-factory",
    "version": "1.0.0",
    ...
  },
  "spec": {
    ...
  },
  "status": {
    "phase": "active",
    "lastUsed": "2026-05-14T10:00:00Z",
    "totalWorkOrders": 47,
    "successRate": 0.92
  }
}
```

---

### Create Factory

```http
POST /factories
```

**Request Body:**

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "FactoryBlueprint",
  "metadata": {
    "name": "my-factory",
    "displayName": "My Factory"
  },
  "spec": {
    "pipelines": [...],
    "tools": [...],
    ...
  }
}
```

**Response:** `201 Created`

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "FactoryBlueprint",
  "metadata": {
    "name": "my-factory",
    "version": "1.0.0",
    "createdAt": "2026-05-14T10:00:00Z"
  },
  ...
}
```

---

### Update Factory

```http
PUT /factories/{name}
```

**Request Body:** Full FactoryBlueprint

**Response:** `200 OK`

---

### Delete Factory

```http
DELETE /factories/{name}
```

**Response:** `204 No Content`

---

### Validate Factory

```http
POST /factories/{name}/validate
```

**Response:**

```json
{
  "valid": true,
  "errors": [],
  "warnings": [
    {
      "path": "spec.pipelines[0].steps[2].timeout",
      "message": "Timeout exceeds recommended maximum of 30m"
    }
  ]
}
```

---

## 3. WorkOrder Management

### Submit WorkOrder

```http
POST /factories/{factory}/workorders
```

**Request Body:**

```json
{
  "metadata": {
    "priority": "normal",
    "labels": {
      "project": "my-project"
    }
  },
  "spec": {
    "goal": "Create a Vulkan rendering module",
    "pipeline": "create-module",
    "inputs": {
      "data": {
        "moduleName": "vulkan_backend",
        "features": ["deferred-shading"]
      }
    },
    "constraints": {
      "maxDuration": "2h",
      "maxCost": 5.00
    }
  }
}
```

**Response:** `202 Accepted`

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "WorkOrder",
  "metadata": {
    "id": "wo-2026-05-14-001",
    "factory": "game-engine-factory",
    "submittedAt": "2026-05-14T10:00:00Z"
  },
  "status": {
    "phase": "pending"
  }
}
```

---

### List WorkOrders

```http
GET /factories/{factory}/workorders
```

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `status` | string | Filter by phase |
| `limit` | integer | Max results |
| `offset` | integer | Pagination offset |

**Response:**

```json
{
  "workOrders": [
    {
      "metadata": {
        "id": "wo-2026-05-14-001",
        "factory": "game-engine-factory"
      },
      "spec": {
        "goal": "Create a Vulkan rendering module"
      },
      "status": {
        "phase": "running",
        "progress": {
          "currentStep": "implement",
          "percentComplete": 60
        }
      }
    }
  ],
  "total": 1
}
```

---

### Get WorkOrder

```http
GET /factories/{factory}/workorders/{id}
```

**Response:** Full WorkOrder with current status

---

### Get WorkOrder Status

```http
GET /factories/{factory}/workorders/{id}/status
```

**Response:**

```json
{
  "phase": "running",
  "startedAt": "2026-05-14T10:01:00Z",
  "progress": {
    "currentStep": "implement",
    "completedSteps": 2,
    "totalSteps": 5,
    "percentComplete": 60
  },
  "estimatedTimeRemaining": "45m"
}
```

---

### Cancel WorkOrder

```http
POST /factories/{factory}/workorders/{id}/cancel
```

**Response:** `200 OK`

```json
{
  "phase": "cancelled",
  "cancelledAt": "2026-05-14T10:30:00Z"
}
```

---

### Stream WorkOrder Events

```http
GET /factories/{factory}/workorders/{id}/events
Accept: text/event-stream
```

**Response:** Server-Sent Events

```
event: started
data: {"step": "analyze", "timestamp": "2026-05-14T10:01:00Z"}

event: step-complete
data: {"step": "analyze", "duration": "45s", "output": {...}}

event: step-start
data: {"step": "design", "timestamp": "2026-05-14T10:01:45Z"}

event: progress
data: {"percentComplete": 40}

event: completed
data: {"artifactReceiptId": "ar-2026-05-14-001"}
```

---

## 4. Artifact Management

### Get Artifact Receipt

```http
GET /artifacts/{id}
```

**Response:** Full ArtifactReceipt

---

### Download Artifact

```http
GET /artifacts/{id}/download
```

**Response:** Binary content with headers:

```http
Content-Type: application/octet-stream
Content-Disposition: attachment; filename="vulkan_backend.tar.gz"
Content-Length: 45678
```

---

### List Artifacts

```http
GET /artifacts
```

**Query Parameters:**

| Parameter | Type | Description |
|-----------|------|-------------|
| `factory` | string | Filter by factory |
| `workOrder` | string | Filter by work order |
| `type` | string | Filter by artifact type |
| `limit` | integer | Max results |

---

### Get Artifact Provenance

```http
GET /artifacts/{id}/provenance
```

**Response:**

```json
{
  "artifactId": "sha256:a3f7b2...",
  "inputs": [
    {"type": "workOrder", "ref": "wo-2026-05-14-001"}
  ],
  "steps": [
    {
      "stepId": "analyze",
      "executor": "model",
      "model": "gpt-4-turbo",
      "duration": "45s",
      "tokensUsed": 4500
    },
    ...
  ],
  "memoryRefs": ["mem-182", "mem-195"],
  "verificationResult": {
    "status": "pass",
    "checks": [...]
  }
}
```

---

### Verify Artifact Signature

```http
POST /artifacts/{id}/verify
```

**Response:**

```json
{
  "valid": true,
  "signature": {
    "method": "cosign",
    "signedAt": "2026-05-14T10:45:01Z",
    "keyId": "heya-production-key"
  }
}
```

---

## 5. Memory Management

### Query Memory

```http
POST /factories/{factory}/memory/query
```

**Request Body:**

```json
{
  "query": "What did we decide about ECS architecture?",
  "tiers": ["episodic", "semantic"],
  "limit": 10
}
```

**Response:**

```json
{
  "results": [
    {
      "id": "mem-182",
      "type": "decision",
      "summary": "Decided to use ECS architecture",
      "content": {
        "topic": "architecture",
        "choice": "ECS over OOP",
        "rationale": "Better cache locality..."
      },
      "relevance": 0.92,
      "createdAt": "2026-04-15T14:00:00Z"
    }
  ]
}
```

---

### Search Memory (Semantic)

```http
POST /factories/{factory}/memory/search
```

**Request Body:**

```json
{
  "text": "rendering performance optimization",
  "tiers": ["semantic"],
  "filters": {
    "tags": ["rendering", "performance"]
  },
  "limit": 20
}
```

**Response:**

```json
{
  "results": [
    {
      "id": "mem-201",
      "type": "pattern",
      "summary": "Render graph pattern for optimization",
      "relevance": 0.88
    }
  ]
}
```

---

### Get Memory Entry

```http
GET /factories/{factory}/memory/{id}
```

**Response:** Full FactoryMemory entry

---

### Write Memory Entry

```http
POST /factories/{factory}/memory
```

**Request Body:**

```json
{
  "spec": {
    "type": "decision",
    "content": {
      "topic": "rendering",
      "choice": "deferred shading",
      "rationale": "Better for many lights"
    },
    "summary": "Chose deferred shading for lighting",
    "tags": ["rendering", "lighting"]
  }
}
```

---

## 6. Webhooks & Events

### Create Webhook

```http
POST /webhooks
```

**Request Body:**

```json
{
  "url": "https://my-app.com/webhooks/heya",
  "events": ["workorder.started", "workorder.completed", "artifact.created"],
  "secret": "my-webhook-secret"
}
```

**Response:**

```json
{
  "id": "wh-001",
  "url": "https://my-app.com/webhooks/heya",
  "events": ["workorder.started", "workorder.completed", "artifact.created"],
  "createdAt": "2026-05-14T10:00:00Z"
}
```

---

### Webhook Payload

**Example: workorder.completed**

```json
{
  "event": "workorder.completed",
  "timestamp": "2026-05-14T10:45:00Z",
  "data": {
    "workOrderId": "wo-2026-05-14-001",
    "factory": "game-engine-factory",
    "artifactReceiptId": "ar-2026-05-14-001",
    "status": "success",
    "duration": "45m"
  },
  "signature": "sha256=..."
}
```

---

### List Webhooks

```http
GET /webhooks
```

---

### Delete Webhook

```http
DELETE /webhooks/{id}
```

---

## 7. Error Reference

### Error Response Format

```json
{
  "error": {
    "code": "WORKORDER_NOT_FOUND",
    "message": "WorkOrder 'wo-123' not found",
    "details": {
      "workOrderId": "wo-123"
    },
    "requestId": "req-abc123"
  }
}
```

### Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `UNAUTHORIZED` | 401 | Missing or invalid authentication |
| `FORBIDDEN` | 403 | Insufficient permissions |
| `NOT_FOUND` | 404 | Resource not found |
| `VALIDATION_ERROR` | 400 | Request validation failed |
| `FACTORY_NOT_FOUND` | 404 | Factory does not exist |
| `WORKORDER_NOT_FOUND` | 404 | WorkOrder does not exist |
| `ARTIFACT_NOT_FOUND` | 404 | Artifact does not exist |
| `FACTORY_INACTIVE` | 400 | Factory is not active |
| `WORKORDER_ALREADY_RUNNING` | 409 | WorkOrder is already running |
| `BUDGET_EXCEEDED` | 402 | Budget limit exceeded |
| `RATE_LIMIT_EXCEEDED` | 429 | Too many requests |
| `INTERNAL_ERROR` | 500 | Internal server error |

---

## 8. Rate Limiting

### Rate Limit Headers

```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 95
X-RateLimit-Reset: 1715678400
```

### Rate Limits

| Endpoint | Limit | Window |
|----------|-------|--------|
| `POST /factories/{factory}/workorders` | 100 | 1 minute |
| `GET /factories/{factory}/workorders/{id}/events` | 10 | 1 minute |
| Other endpoints | 1000 | 1 minute |

---

## Next Steps

- **[Getting Started](./06-getting-started.md)**: Quick start tutorial
