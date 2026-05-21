# Getting Started

> This guide walks you through setting up Heya and creating your first factory.

---

## 1. Prerequisites

### Required

- **Node.js** 20.x or later
- **pnpm** 8.x or later
- **Docker** and Docker Compose (for local infrastructure)
- **Git**

### Optional

- **Temporal CLI** (for workflow development)
- **Rust** (if building Rust-focused factories)
- **Python** 3.11+ (if using Python tools)

### Verify Prerequisites

```bash
node --version   # Should be 20.x or later
pnpm --version   # Should be 8.x or later
docker --version # Should be installed
```

---

## 2. Installation

### Clone the Repository

```bash
git clone https://github.com/Oasis-Company/HeyaLab.git
cd HeyaLab
```

### Install Dependencies

```bash
pnpm install
```

### Start Local Infrastructure

```bash
# Start PostgreSQL, Redis, and Temporal
docker-compose up -d

# Wait for services to be ready
docker-compose ps
```

Expected output:

```
NAME                STATUS              PORTS
heya-postgres       running             0.0.0.0:5432->5432/tcp
heya-redis          running             0.0.0.0:6379->6379/tcp
heya-temporal       running             0.0.0.0:7233->7233/tcp
```

### Run Database Migrations

```bash
pnpm db:migrate
```

### Start the API Server

```bash
pnpm dev
```

The API should now be available at `http://localhost:8080`.

---

## 3. Quick Start Tutorial

### Step 1: Create Your First Factory

Let's create a simple factory that generates code modules.

**Using the API:**

```bash
curl -X POST http://localhost:8080/v1/factories \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer dev-token" \
  -d '{
    "apiVersion": "heya.io/v1",
    "kind": "FactoryBlueprint",
    "metadata": {
      "name": "my-first-factory",
      "displayName": "My First Factory",
      "description": "A simple factory for generating code modules"
    },
    "spec": {
      "pipelines": [
        {
          "id": "generate",
          "name": "Generate Module",
          "steps": [
            {
              "id": "design",
              "action": "Design the module structure",
              "executor": { "type": "model", "capability": "design" }
            },
            {
              "id": "implement",
              "action": "Generate the code",
              "dependsOn": ["design"],
              "executor": { "type": "model", "capability": "codegen" }
            },
            {
              "id": "verify",
              "action": "Verify the output",
              "dependsOn": ["implement"],
              "executor": { "type": "subroutine", "capability": "verify" }
            }
          ]
        }
      ],
      "tools": [],
      "verification": {
        "defaultLevel": 0,
        "checks": [
          { "type": "syntax", "required": true }
        ]
      },
      "memory": {
        "working": { "ttl": "1h" },
        "episodic": { "retention": "30d" }
      },
      "routing": {
        "strategy": "balanced",
        "providers": [
          {
            "name": "openai",
            "models": ["gpt-4o", "gpt-4o-mini"],
            "priority": 1
          }
        ]
      },
      "policies": []
    }
  }'
```

**Response:**

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "FactoryBlueprint",
  "metadata": {
    "name": "my-first-factory",
    "version": "1.0.0",
    "displayName": "My First Factory",
    "createdAt": "2026-05-14T10:00:00Z"
  },
  "status": {
    "phase": "active"
  }
}
```

---

### Step 2: Submit a WorkOrder

Now let's use the factory to create something.

```bash
curl -X POST http://localhost:8080/v1/factories/my-first-factory/workorders \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer dev-token" \
  -d '{
    "metadata": {
      "priority": "normal"
    },
    "spec": {
      "goal": "Create a TypeScript utility module for date formatting with functions for parsing, formatting, and relative time",
      "pipeline": "generate",
      "inputs": {
        "data": {
          "language": "typescript",
          "moduleName": "date-utils",
          "features": ["parse", "format", "relative"]
        }
      }
    }
  }'
```

**Response:**

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "WorkOrder",
  "metadata": {
    "id": "wo-2026-05-14-001",
    "factory": "my-first-factory",
    "submittedAt": "2026-05-14T10:01:00Z"
  },
  "status": {
    "phase": "pending"
  }
}
```

---

### Step 3: Monitor Progress

**Check status:**

```bash
curl http://localhost:8080/v1/factories/my-first-factory/workorders/wo-2026-05-14-001/status \
  -H "Authorization: Bearer dev-token"
```

**Response:**

```json
{
  "phase": "running",
  "startedAt": "2026-05-14T10:01:00Z",
  "progress": {
    "currentStep": "implement",
    "completedSteps": 1,
    "totalSteps": 3,
    "percentComplete": 50
  },
  "estimatedTimeRemaining": "30s"
}
```

**Stream events (optional):**

```bash
curl -N http://localhost:8080/v1/factories/my-first-factory/workorders/wo-2026-05-14-001/events \
  -H "Authorization: Bearer dev-token" \
  -H "Accept: text/event-stream"
```

---

### Step 4: Get the Result

Once the WorkOrder completes, retrieve the artifact:

```bash
curl http://localhost:8080/v1/factories/my-first-factory/workorders/wo-2026-05-14-001 \
  -H "Authorization: Bearer dev-token"
```

**Response:**

```json
{
  "status": {
    "phase": "completed",
    "completedAt": "2026-05-14T10:02:30Z"
  },
  "result": {
    "artifactReceiptId": "ar-2026-05-14-001"
  }
}
```

**Get the artifact receipt:**

```bash
curl http://localhost:8080/v1/artifacts/ar-2026-05-14-001 \
  -H "Authorization: Bearer dev-token"
```

**Response:**

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "ArtifactReceipt",
  "metadata": {
    "id": "ar-2026-05-14-001",
    "workOrder": "wo-2026-05-14-001"
  },
  "spec": {
    "artifact": {
      "digest": "sha256:abc123...",
      "type": "typescript_module",
      "name": "date-utils"
    },
    "verification": {
      "status": "pass",
      "checks": [
        { "type": "syntax", "status": "pass" }
      ]
    }
  }
}
```

**Download the artifact:**

```bash
curl http://localhost:8080/v1/artifacts/ar-2026-05-14-001/download \
  -H "Authorization: Bearer dev-token" \
  -o date-utils.tar.gz
```

---

## 4. Understanding Factory Output

### Artifact Receipt Fields

| Field | Description |
|-------|-------------|
| `artifact.digest` | Content-addressable hash (SHA-256) |
| `artifact.type` | Type of artifact (code, module, document, etc.) |
| `provenance.steps` | How the artifact was created |
| `provenance.memoryRefs` | Memory entries that influenced creation |
| `verification.status` | Overall verification result |
| `verification.checks` | Individual check results |
| `signature` | Cryptographic signature |

### Provenance

The provenance field tells you exactly how the artifact was created:

```json
{
  "provenance": {
    "inputs": [
      { "type": "workOrder", "ref": "wo-2026-05-14-001" }
    ],
    "steps": [
      {
        "stepId": "design",
        "executor": "model",
        "model": "gpt-4o",
        "tokensUsed": 2500
      },
      {
        "stepId": "implement",
        "executor": "model",
        "model": "gpt-4o",
        "tokensUsed": 4500
      }
    ],
    "memoryRefs": []
  }
}
```

---

## 5. Troubleshooting

### Common Issues

**"Factory not found"**

```bash
# Check if factory exists
curl http://localhost:8080/v1/factories \
  -H "Authorization: Bearer dev-token"
```

**"WorkOrder stuck in pending"**

```bash
# Check factory status
curl http://localhost:8080/v1/factories/my-first-factory \
  -H "Authorization: Bearer dev-token"

# Check infrastructure
docker-compose ps
```

**"Model API error"**

- Verify `OPENAI_API_KEY` or `ANTHROPIC_API_KEY` is set
- Check API key has sufficient quota

### Logs

```bash
# API server logs
pnpm dev

# Docker logs
docker-compose logs -f

# Temporal logs
docker-compose logs -f heya-temporal
```

### Debug Mode

```bash
# Enable debug logging
LOG_LEVEL=debug pnpm dev
```

---

## 6. Where to Go Next

### Learn More

- **[Factory Concept](./01-factory-concept.md)** - Understand the "AI Factory" metaphor
- **[Core Architecture](./02-core-architecture.md)** - Deep dive into components
- **[Core Contracts](./03-core-contracts.md)** - Formal specifications
- **[Factory Design Guide](./04-factory-design-guide.md)** - Design your own factory

### Try More Examples

**Game Engine Factory:**

```bash
# See the complete example in the design guide
# docs/04-factory-design-guide.md#complete-example
```

**Analysis Factory:**

```yaml
# Create a factory for codebase analysis
pipelines:
  - id: analyze
    steps:
      - id: parse
        action: Parse codebase
        executor: { type: tool, tool: code-indexer }
      - id: analyze
        action: Analyze patterns
        executor: { type: model, capability: analysis }
      - id: report
        action: Generate report
        executor: { type: model, capability: documentation }
```

### Join the Community

- **GitHub**: https://github.com/Oasis-Company/HeyaLab
- **Discord**: https://discord.gg/heya
- **Twitter**: @HeyaAI

---

## 7. Environment Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `PORT` | API server port | `8080` |
| `DATABASE_URL` | PostgreSQL connection | `postgresql://localhost:5432/heya` |
| `REDIS_URL` | Redis connection | `redis://localhost:6379` |
| `TEMPORAL_ADDRESS` | Temporal server | `localhost:7233` |
| `OPENAI_API_KEY` | OpenAI API key | (required) |
| `ANTHROPIC_API_KEY` | Anthropic API key | (optional) |
| `LOG_LEVEL` | Logging level | `info` |

---

## 8. Development Tips

### Hot Reload

The development server supports hot reload:

```bash
pnpm dev
# Edit files and see changes automatically
```

### Testing

```bash
# Run all tests
pnpm test

# Run specific test file
pnpm test src/__tests__/factory.test.ts

# Run with coverage
pnpm test:coverage
```

### Linting

```bash
# Check for issues
pnpm lint

# Fix issues
pnpm lint:fix
```

### Type Checking

```bash
pnpm typecheck
```
