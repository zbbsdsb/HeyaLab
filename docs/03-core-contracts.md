# Core Contracts

> This document defines the core contract objects that govern all Heya Factory operations.

---

## 1. Contract Overview

Heya Factory operations are governed by four core contracts:

| Contract | Purpose | Created By | Lifecycle |
|----------|---------|------------|-----------|
| **FactoryBlueprint** | AI-derived factory structure from user intent | AI (proposed) → Human (approved) | Design → Approve → Activate → Evolve |
| **WorkOrder** | A task for the factory to execute | Human (submitted) → AI (executed) | Submit → Execute → Verify → Complete |
| **ArtifactReceipt** | Proof of creation and verification | AI (produced) → Human (approved) | Create → Verify → Approve → Store |
| **FactoryMemory** | Accumulated experience and knowledge | AI (written) + Human (decisions) | Write → Query → Consolidate → Archive |

**Key difference from traditional systems**: Users never directly create or edit these contracts. They describe intent in natural language, and the AI manages the contracts internally. Contracts are exposed for transparency, debugging, and programmatic access.

---

## 2. FactoryBlueprint

### 2.1 Purpose

The `FactoryBlueprint` is the AI's self-designed operating structure. When a user says "I want to build X", the AI:

1. Analyzes the intent
2. Derives required capabilities
3. Generates a FactoryBlueprint
4. Presents it to the human for approval
5. Activates the factory once approved

### 2.2 JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://heya.io/schemas/factory-blueprint/v1",
  "title": "FactoryBlueprint",
  "description": "AI-derived factory structure based on user intent",
  "type": "object",
  "required": ["apiVersion", "kind", "metadata", "spec"],
  "properties": {
    "apiVersion": {
      "type": "string",
      "const": "heya.io/v1"
    },
    "kind": {
      "type": "string",
      "const": "FactoryBlueprint"
    },
    "metadata": {
      "type": "object",
      "required": ["name", "version"],
      "properties": {
        "name": {
          "type": "string",
          "pattern": "^[a-z0-9-]+$",
          "description": "Unique factory identifier"
        },
        "version": {
          "type": "string",
          "pattern": "^\\d+\\.\\d+\\.\\d+$"
        },
        "displayName": {
          "type": "string",
          "description": "Human-readable name"
        },
        "description": {
          "type": "string",
          "description": "What this factory creates"
        },
        "owner": {
          "type": "string",
          "description": "User or team that owns this factory"
        },
        "createdAt": { "type": "string", "format": "date-time" },
        "updatedAt": { "type": "string", "format": "date-time" }
      }
    },
    "spec": {
      "type": "object",
      "required": ["intent", "capabilities", "humanCheckpoints", "policies"],
      "properties": {
        "intent": {
          "type": "object",
          "description": "The original user intent that drove this design",
          "properties": {
            "description": {
              "type": "string",
              "description": "User's original request in their own words"
            },
            "domain": {
              "type": "string",
              "description": "Derived domain (e.g., game-development, marketing, research)"
            },
            "productType": {
              "type": "string",
              "description": "Type of product (software, design, content, strategy, knowledge, physical)"
            },
            "complexity": {
              "enum": ["low", "medium", "high", "extreme"]
            },
            "clarifications": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "question": { "type": "string" },
                  "answer": { "type": "string" }
                }
              }
            }
          }
        },
        "capabilities": {
          "type": "object",
          "description": "AI capabilities assembled for this factory",
          "properties": {
            "reasoning": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "strategy": {
                    "enum": ["architect", "implement", "analyze", "debug", "review"]
                  },
                  "description": { "type": "string" },
                  "modelPreference": { "type": "string" }
                }
              }
            },
            "tools": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "id": { "type": "string" },
                  "purpose": { "type": "string" },
                  "riskLevel": { "enum": ["read", "write", "deploy", "external"] }
                }
              }
            },
            "verification": {
              "type": "object",
              "properties": {
                "levels": {
                  "type": "array",
                  "items": {
                    "type": "object",
                    "properties": {
                      "level": { "type": "integer", "minimum": 0, "maximum": 4 },
                      "type": { "type": "string" },
                      "required": { "type": "boolean" },
                      "config": { "type": "object" }
                    }
                  }
                },
                "autoFix": { "type": "boolean", "description": "Whether AI should auto-fix minor issues" }
              }
            }
          }
        },
        "humanCheckpoints": {
          "type": "array",
          "description": "Points where human input is required",
          "items": {
            "type": "object",
            "required": ["type", "trigger"],
            "properties": {
              "type": {
                "enum": ["design_approval", "architecture_decision", "critical_output", "direction_change", "budget_alert"]
              },
              "trigger": {
                "type": "string",
                "description": "When this checkpoint activates"
              },
              "contextProvided": {
                "type": "array",
                "items": { "type": "string" },
                "description": "What information the human will see"
              },
              "options": {
                "type": "array",
                "items": { "type": "string" },
                "description": "Available responses (approve, modify, reject, defer)"
              }
            }
          }
        },
        "memory": {
          "type": "object",
          "properties": {
            "working": {
              "type": "object",
              "properties": {
                "ttl": { "type": "string" }
              }
            },
            "episodic": {
              "type": "object",
              "properties": {
                "retention": { "type": "string" }
              }
            },
            "semantic": {
              "type": "object",
              "properties": {
                "embeddingModel": { "type": "string" }
              }
            }
          }
        },
        "modelRouting": {
          "type": "object",
          "properties": {
            "strategy": { "enum": ["balanced", "quality_first", "cost_first", "speed_first"] },
            "rules": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "match": { "type": "object" },
                  "prefer": { "type": "array", "items": { "type": "string" } }
                }
              }
            }
          }
        },
        "policies": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "id": { "type": "string" },
              "type": { "enum": ["budget", "quality", "security", "behavior"] },
              "spec": { "type": "object" },
              "enforcement": { "enum": ["strict", "advisory"] }
            }
          }
        }
      }
    },
    "status": {
      "type": "object",
      "properties": {
        "phase": {
          "enum": ["proposed", "approved", "active", "evolving", "archived"]
        },
        "approvedBy": { "type": "string" },
        "approvedAt": { "type": "string", "format": "date-time" },
        "totalWorkOrders": { "type": "integer" },
        "successRate": { "type": "number" },
        "evolutionCount": { "type": "integer" }
      }
    }
  }
}
```

### 2.3 Example Instance

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "FactoryBlueprint",
  "metadata": {
    "name": "game-engine-factory",
    "version": "1.0.0",
    "displayName": "Game Engine Factory",
    "description": "Factory for building a cross-platform game engine in Rust",
    "owner": "user@example.com"
  },
  "spec": {
    "intent": {
      "description": "I want to build a game engine from scratch",
      "domain": "game-development",
      "productType": "software",
      "complexity": "extreme",
      "clarifications": [
        { "question": "Rendering backends?", "answer": "Vulkan and D3D12" },
        { "question": "Platforms?", "answer": "Windows and Linux" },
        { "question": "Editor needed?", "answer": "Runtime only for now" }
      ]
    },
    "capabilities": {
      "reasoning": [
        { "strategy": "architect", "description": "System architecture and API design", "modelPreference": "claude-opus-4" },
        { "strategy": "implement", "description": "Rust code generation", "modelPreference": "claude-sonnet-4" },
        { "strategy": "analyze", "description": "Codebase analysis and pattern detection", "modelPreference": "gpt-4o" },
        { "strategy": "debug", "description": "Issue diagnosis and resolution", "modelPreference": "claude-sonnet-4" }
      ],
      "tools": [
        { "id": "cargo-build", "purpose": "Build Rust projects", "riskLevel": "write" },
        { "id": "cargo-test", "purpose": "Run tests", "riskLevel": "read" },
        { "id": "cargo-bench", "purpose": "Performance benchmarks", "riskLevel": "read" }
      ],
      "verification": {
        "levels": [
          { "level": 0, "type": "self-check", "required": true },
          { "level": 1, "type": "compile", "required": true },
          { "level": 2, "type": "unit-test", "required": true },
          { "level": 3, "type": "benchmark", "required": false },
          { "level": 4, "type": "human-review", "required": true }
        ],
        "autoFix": true
      }
    },
    "humanCheckpoints": [
      {
        "type": "design_approval",
        "trigger": "Before starting a new major component",
        "contextProvided": ["architecture-plan", "capability-needs", "estimated-cost"],
        "options": ["approve", "modify", "reject"]
      },
      {
        "type": "architecture_decision",
        "trigger": "When multiple valid approaches exist",
        "contextProvided": ["options", "trade-offs", "ai-recommendation", "memory-context"],
        "options": ["choose", "modify", "defer"]
      },
      {
        "type": "critical_output",
        "trigger": "Before releasing a significant artifact",
        "contextProvided": ["artifact", "verification-results", "ai-assessment"],
        "options": ["approve", "request-changes", "reject"]
      }
    ],
    "memory": {
      "working": { "ttl": "2h" },
      "episodic": { "retention": "180d" },
      "semantic": { "embeddingModel": "text-embedding-3-small" }
    },
    "modelRouting": {
      "strategy": "balanced",
      "rules": [
        { "match": { "strategy": "architect" }, "prefer": ["claude-opus-4", "gpt-5"] },
        { "match": { "strategy": "implement" }, "prefer": ["claude-sonnet-4", "gpt-4o"] }
      ]
    },
    "policies": [
      { "id": "budget", "type": "budget", "spec": { "maxCostPerTask": 10.00 }, "enforcement": "strict" },
      { "id": "quality", "type": "quality", "spec": { "minVerificationLevel": 2 }, "enforcement": "strict" }
    ]
  },
  "status": {
    "phase": "approved",
    "approvedBy": "user@example.com",
    "approvedAt": "2026-05-15T10:00:00Z"
  }
}
```

---

## 3. WorkOrder

### 3.1 Purpose

A `WorkOrder` is a task submitted by a human for the factory to execute. The AI executes the task using the factory's assembled capabilities, with human check-ins at defined checkpoints.

### 3.2 JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://heya.io/schemas/work-order/v1",
  "title": "WorkOrder",
  "description": "A task for the Heya Factory to execute",
  "type": "object",
  "required": ["apiVersion", "kind", "metadata", "spec"],
  "properties": {
    "apiVersion": { "type": "string", "const": "heya.io/v1" },
    "kind": { "type": "string", "const": "WorkOrder" },
    "metadata": {
      "type": "object",
      "required": ["id", "factory"],
      "properties": {
        "id": { "type": "string" },
        "factory": { "type": "string" },
        "submittedBy": { "type": "string" },
        "submittedAt": { "type": "string", "format": "date-time" },
        "priority": { "enum": ["low", "normal", "high", "urgent"] }
      }
    },
    "spec": {
      "type": "object",
      "required": ["goal"],
      "properties": {
        "goal": {
          "type": "string",
          "description": "What this work order should accomplish (natural language)"
        },
        "context": {
          "type": "object",
          "description": "Additional context provided by the human",
          "properties": {
            "files": { "type": "array", "items": { "type": "string" } },
            "data": { "type": "object" },
            "references": { "type": "array", "items": { "type": "string" } }
          }
        },
        "constraints": {
          "type": "object",
          "properties": {
            "maxDuration": { "type": "string" },
            "maxCost": { "type": "number" }
          }
        }
      }
    },
    "status": {
      "type": "object",
      "properties": {
        "phase": {
          "enum": ["pending", "executing", "awaiting_human", "verifying", "completed", "failed", "cancelled"]
        },
        "startedAt": { "type": "string", "format": "date-time" },
        "completedAt": { "type": "string", "format": "date-time" },
        "progress": {
          "type": "object",
          "properties": {
            "description": { "type": "string" },
            "percentComplete": { "type": "number" }
          }
        },
        "currentCheckpoint": {
          "type": "object",
          "properties": {
            "type": { "type": "string" },
            "context": { "type": "object" },
            "awaitingSince": { "type": "string", "format": "date-time" }
          },
          "description": "If phase is 'awaiting_human', describes what the AI needs"
        },
        "humanInteractions": {
          "type": "array",
          "items": {
            "type": "object",
            "properties": {
              "checkpointType": { "type": "string" },
              "presentedAt": { "type": "string", "format": "date-time" },
              "respondedAt": { "type": "string", "format": "date-time" },
              "decision": { "type": "string" },
              "feedback": { "type": "string" }
            }
          }
        }
      }
    }
  }
}
```

### 3.3 Example Instance

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "WorkOrder",
  "metadata": {
    "id": "wo-2026-05-15-001",
    "factory": "game-engine-factory",
    "submittedBy": "user@example.com",
    "submittedAt": "2026-05-15T10:30:00Z",
    "priority": "normal"
  },
  "spec": {
    "goal": "Create a Vulkan rendering backend module with deferred shading support",
    "context": {
      "data": {
        "language": "rust",
        "features": ["deferred-shading", "PBR", "shadow-mapping"]
      }
    },
    "constraints": {
      "maxDuration": "2h",
      "maxCost": 5.00
    }
  },
  "status": {
    "phase": "awaiting_human",
    "startedAt": "2026-05-15T10:30:05Z",
    "progress": {
      "description": "Architecture designed, awaiting approval before implementation",
      "percentComplete": 25
    },
    "currentCheckpoint": {
      "type": "design_approval",
      "context": {
        "proposedArchitecture": "ECS-based with deferred rendering pipeline",
        "keyDecisions": ["G-buffer layout: RGBA16F normals + RGB10A2 albedo + R11G11B10 depth"],
        "estimatedCost": "$2.50 for implementation phase"
      },
      "awaitingSince": "2026-05-15T10:32:00Z"
    }
  }
}
```

---

## 4. ArtifactReceipt

### 4.1 Purpose

An `ArtifactReceipt` proves that an artifact was created by the AI, verified automatically, and approved by the human. It provides full traceability.

### 4.2 JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://heya.io/schemas/artifact-receipt/v1",
  "title": "ArtifactReceipt",
  "description": "Proof of artifact creation, verification, and human approval",
  "type": "object",
  "required": ["apiVersion", "kind", "metadata", "spec"],
  "properties": {
    "apiVersion": { "type": "string", "const": "heya.io/v1" },
    "kind": { "type": "string", "const": "ArtifactReceipt" },
    "metadata": {
      "type": "object",
      "required": ["id", "workOrder"],
      "properties": {
        "id": { "type": "string" },
        "workOrder": { "type": "string" },
        "factory": { "type": "string" },
        "createdAt": { "type": "string", "format": "date-time" }
      }
    },
    "spec": {
      "type": "object",
      "required": ["artifact", "provenance", "verification", "humanApproval"],
      "properties": {
        "artifact": {
          "type": "object",
          "required": ["digest", "type", "name"],
          "properties": {
            "digest": { "type": "string", "description": "Content-addressable hash" },
            "type": { "type": "string", "description": "Product type (code, design, content, strategy, etc.)" },
            "name": { "type": "string" },
            "size": { "type": "integer" },
            "uri": { "type": "string" }
          }
        },
        "provenance": {
          "type": "object",
          "properties": {
            "intent": { "type": "string", "description": "Original goal" },
            "memoryRefs": { "type": "array", "items": { "type": "string" } },
            "humanDecisions": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "checkpoint": { "type": "string" },
                  "decision": { "type": "string" },
                  "feedback": { "type": "string" }
                }
              }
            },
            "modelsUsed": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "model": { "type": "string" },
                  "purpose": { "type": "string" },
                  "tokensUsed": { "type": "integer" }
                }
              }
            }
          }
        },
        "verification": {
          "type": "object",
          "required": ["status", "checks"],
          "properties": {
            "status": { "enum": ["pass", "fail", "partial"] },
            "checks": {
              "type": "array",
              "items": {
                "type": "object",
                "properties": {
                  "level": { "type": "integer" },
                  "type": { "type": "string" },
                  "status": { "enum": ["pass", "fail", "skip"] },
                  "details": { "type": "object" }
                }
              }
            }
          }
        },
        "humanApproval": {
          "type": "object",
          "required": ["approvedBy", "decision"],
          "properties": {
            "approvedBy": { "type": "string" },
            "decision": { "enum": ["approved", "approved_with_notes", "changes_requested"] },
            "feedback": { "type": "string" },
            "approvedAt": { "type": "string", "format": "date-time" }
          }
        },
        "cost": {
          "type": "object",
          "properties": {
            "modelTokens": { "type": "integer" },
            "totalUsd": { "type": "number" }
          }
        }
      }
    }
  }
}
```

### 4.3 Example Instance

```json
{
  "apiVersion": "heya.io/v1",
  "kind": "ArtifactReceipt",
  "metadata": {
    "id": "ar-2026-05-15-001",
    "workOrder": "wo-2026-05-15-001",
    "factory": "game-engine-factory",
    "createdAt": "2026-05-15T11:15:00Z"
  },
  "spec": {
    "artifact": {
      "digest": "sha256:a3f7b2c8d9e1f4a5b6c7d8e9f0a1b2c3",
      "type": "rust_module",
      "name": "vulkan_backend",
      "size": 45678,
      "uri": "oci://registry.heya.io/artifacts/vulkan_backend@sha256:a3f7b2"
    },
    "provenance": {
      "intent": "Create a Vulkan rendering backend module with deferred shading support",
      "memoryRefs": ["mem-182", "mem-195", "mem-201"],
      "humanDecisions": [
        {
          "checkpoint": "design_approval",
          "decision": "approved",
          "feedback": "Looks good, but make sure the G-buffer is configurable"
        },
        {
          "checkpoint": "critical_output",
          "decision": "approved_with_notes",
          "feedback": "Great work. Add more comments to the descriptor set layout."
        }
      ],
      "modelsUsed": [
        { "model": "claude-opus-4", "purpose": "architecture", "tokensUsed": 4500 },
        { "model": "claude-sonnet-4", "purpose": "implementation", "tokensUsed": 45000 }
      ]
    },
    "verification": {
      "status": "pass",
      "checks": [
        { "level": 0, "type": "self-check", "status": "pass" },
        { "level": 1, "type": "compile", "status": "pass", "details": { "duration": "3.2s" } },
        { "level": 2, "type": "unit-test", "status": "pass", "details": { "passed": 23, "failed": 0 } },
        { "level": 3, "type": "benchmark", "status": "pass", "details": { "p95_ms": 12.4 } },
        { "level": 4, "type": "human-review", "status": "pass" }
      ]
    },
    "humanApproval": {
      "approvedBy": "user@example.com",
      "decision": "approved_with_notes",
      "feedback": "Great work. Add more comments to the descriptor set layout.",
      "approvedAt": "2026-05-15T11:14:30Z"
    },
    "cost": {
      "modelTokens": 49500,
      "totalUsd": 1.23
    }
  }
}
```

---

## 5. FactoryMemory

### 5.1 Purpose

`FactoryMemory` is the accumulated experience that makes the factory smarter. It includes AI-generated insights and human decisions.

### 5.2 JSON Schema

```json
{
  "$schema": "https://json-schema.org/draft/2020-12/schema",
  "$id": "https://heya.io/schemas/factory-memory/v1",
  "title": "FactoryMemory",
  "description": "Factory experience and knowledge",
  "type": "object",
  "required": ["apiVersion", "kind", "metadata", "spec"],
  "properties": {
    "apiVersion": { "type": "string", "const": "heya.io/v1" },
    "kind": { "type": "string", "const": "FactoryMemory" },
    "metadata": {
      "type": "object",
      "required": ["id", "factory", "tier"],
      "properties": {
        "id": { "type": "string" },
        "factory": { "type": "string" },
        "tier": { "enum": ["working", "episodic", "semantic", "artifact"] },
        "createdAt": { "type": "string", "format": "date-time" },
        "expiresAt": { "type": "string", "format": "date-time" }
      }
    },
    "spec": {
      "type": "object",
      "required": ["type", "content", "source"],
      "properties": {
        "type": {
          "enum": ["decision", "failure", "pattern", "preference", "fact", "human_feedback"]
        },
        "content": { "type": "object" },
        "summary": { "type": "string" },
        "source": {
          "type": "object",
          "properties": {
            "origin": { "enum": ["ai", "human", "system"] },
            "workOrder": { "type": "string" },
            "artifact": { "type": "string" },
            "user": { "type": "string" }
          }
        },
        "tags": { "type": "array", "items": { "type": "string" } },
        "related": { "type": "array", "items": { "type": "string" } },
        "accessCount": { "type": "integer", "description": "How often this memory was retrieved" },
        "lastAccessedAt": { "type": "string", "format": "date-time" }
      }
    }
  }
}
```

### 5.3 Example Instances

**Human Decision:**
```json
{
  "apiVersion": "heya.io/v1",
  "kind": "FactoryMemory",
  "metadata": {
    "id": "mem-182",
    "factory": "game-engine-factory",
    "tier": "episodic",
    "createdAt": "2026-04-15T14:00:00Z"
  },
  "spec": {
    "type": "decision",
    "content": {
      "topic": "architecture",
      "choice": "ECS over OOP",
      "rationale": "Better cache locality, easier parallelization",
      "alternatives": ["OOP inheritance", "Component-based without ECS"]
    },
    "summary": "Decided to use ECS architecture for better performance",
    "source": {
      "origin": "human",
      "user": "user@example.com",
      "workOrder": "wo-2026-04-15-003"
    },
    "tags": ["architecture", "ecs", "performance"],
    "accessCount": 12
  }
}
```

**Human Feedback:**
```json
{
  "apiVersion": "heya.io/v1",
  "kind": "FactoryMemory",
  "metadata": {
    "id": "mem-250",
    "factory": "game-engine-factory",
    "tier": "episodic"
  },
  "spec": {
    "type": "human_feedback",
    "content": {
      "about": "code_style",
      "preference": "More comments in complex shader code",
      "reason": "Team members need to understand the rendering pipeline"
    },
    "summary": "User prefers detailed comments in shader code",
    "source": {
      "origin": "human",
      "user": "user@example.com",
      "workOrder": "wo-2026-05-15-001"
    },
    "tags": ["style", "comments", "shaders"]
  }
}
```

---

## 6. Contract Interactions

### 6.1 Lifecycle Flow

```
User Intent
    │
    ▼
AI creates FactoryBlueprint (proposed)
    │
    ▼
Human approves → FactoryBlueprint (active)
    │
    ▼
Human submits WorkOrder
    │
    ▼
AI executes (reads/writes FactoryMemory)
    │
    ▼
AI reaches Human Checkpoint → Human responds
    │
    ▼
AI produces output → Auto-verification
    │
    ▼
Human approves → ArtifactReceipt issued
    │
    ▼
Experience stored in FactoryMemory
```

### 6.2 Reference Relationships

| From | To | Relationship |
|------|-----|--------------|
| WorkOrder | FactoryBlueprint | "executed by" |
| ArtifactReceipt | WorkOrder | "produced by" |
| ArtifactReceipt | FactoryMemory | "influenced by" |
| ArtifactReceipt.provenance.humanDecisions | FactoryMemory | "records human input" |
| FactoryBlueprint | FactoryMemory | "evolves from" |

---

## Next Steps

- **[Factory Design Guide](./04-factory-design-guide.md)**: How to create factories through conversation
- **[API Reference](./05-api-reference.md)**: REST APIs for managing contracts
