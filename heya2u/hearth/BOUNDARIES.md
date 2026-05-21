# Hearth Boundaries

> Boundaries define what is possible. Within them, everything is permitted.

---

## Boundary Philosophy

Hearth does not prescribe what agents should do—it defines what they cannot do.

```
Traditional System:                    Hearth:
┌─────────────────┐                    ┌─────────────────┐
│  Prescribed     │                    │   Boundaries    │
│  Workflows      │                    │   (Limits)      │
│                 │                    │                 │
│  Step 1 ──────► │                    │  ┌───────────┐  │
│  Step 2 ──────► │                    │  │  Agent    │  │
│  Step 3 ──────► │                    │  │  Freedom  │  │
│  Step 4 ──────► │                    │  │           │  │
│                 │                    │  │  Create   │  │
│  Fixed path     │                    │  │  Invent   │  │
│  Limited scope  │                    │  │  Evolve   │  │
└─────────────────┘                    │  └───────────┘  │
                                       │                 │
                                       │  Infinite       │
                                       │  possibilities  │
                                       └─────────────────┘
```

### The Freedom-Constraint Balance

| Aspect | Freedom | Constraint |
|--------|---------|------------|
| **Component Creation** | Agents invent any component needed | Must pass boundary validation |
| **Interactions** | Any component can interact with any other | Must respect capability boundaries |
| **Evolution** | Components can mutate freely | Fitness evaluation determines survival |
| **Resource Use** | Components request what they need | Resource boundaries enforce limits |
| **State Access** | Full read/write to state graph | Semantic boundaries ensure consistency |

---

## Boundary Types

### 1. Safety Boundaries (Hard)

**Purpose**: Prevent harm to user, system, or data.

```typescript
interface SafetyBoundary extends Boundary {
  type: 'safety';
  enforcement: 'hard';  // Never overrideable
  
  rules: {
    // Execution safety
    noArbitraryCodeExecution: boolean;
    noSystemCalls: boolean;
    noNetworkAccessWithoutPermission: boolean;
    
    // Data safety
    noDataExfiltration: boolean;
    noUnauthorizedDataAccess: boolean;
    encryptionRequired: boolean;
    
    // User safety
    noDeceptivePractices: boolean;
    noHarmfulContentGeneration: boolean;
    explicitConsentRequired: string[];
  };
}
```

**Examples**:

```typescript
// Safety Boundary: No Arbitrary Code Execution
const noCodeExecBoundary: SafetyBoundary = {
  id: 'safety-no-code-exec',
  type: 'safety',
  enforcement: 'hard',
  rules: {
    noArbitraryCodeExecution: true,
    allowedExecutionContexts: ['sandboxed-wasm', 'interpreted-safe'],
    prohibitedOperations: ['eval', 'Function', 'exec', 'spawn']
  }
};

// Safety Boundary: Data Privacy
const dataPrivacyBoundary: SafetyBoundary = {
  id: 'safety-data-privacy',
  type: 'safety',
  enforcement: 'hard',
  rules: {
    noDataExfiltration: true,
    dataRetentionLimit: 'session-only',
    encryptionRequired: true,
    allowedDataDestinations: ['local-storage', 'user-approved-cloud']
  }
};
```

---

### 2. Resource Boundaries (Hard/Soft)

**Purpose**: Manage computational resources fairly.

```typescript
interface ResourceBoundary extends Boundary {
  type: 'resource';
  enforcement: 'hard' | 'soft';
  
  limits: {
    compute: {
      maxCpuTime: number;        // milliseconds
      maxConcurrentExecutions: number;
      priorityLevels: number;
    };
    memory: {
      maxHeapSize: number;       // bytes
      maxStackSize: number;
      maxTotalAllocation: number;
    };
    storage: {
      maxPersistentStorage: number;
      maxTempStorage: number;
      maxObjectSize: number;
    };
    network: {
      maxBandwidth: number;      // bytes/sec
      maxConnections: number;
      allowedProtocols: string[];
      blockedHosts: string[];
    };
  };
  
  // Soft boundary: what happens when exceeded
  onExceedSoft: 'throttle' | 'warn' | 'queue' | 'request-more';
}
```

**Examples**:

```typescript
// Resource Boundary: Standard Project
const standardProjectBoundary: ResourceBoundary = {
  id: 'resource-standard',
  type: 'resource',
  enforcement: 'soft',
  limits: {
    compute: {
      maxCpuTime: 60000,        // 1 minute per execution
      maxConcurrentExecutions: 10,
      priorityLevels: 5
    },
    memory: {
      maxHeapSize: 512 * 1024 * 1024,  // 512 MB
      maxStackSize: 8 * 1024 * 1024,   // 8 MB
      maxTotalAllocation: 1024 * 1024 * 1024  // 1 GB
    },
    storage: {
      maxPersistentStorage: 100 * 1024 * 1024,  // 100 MB
      maxTempStorage: 500 * 1024 * 1024,        // 500 MB
      maxObjectSize: 50 * 1024 * 1024           // 50 MB
    },
    network: {
      maxBandwidth: 1024 * 1024,  // 1 MB/s
      maxConnections: 10,
      allowedProtocols: ['https', 'wss'],
      blockedHosts: ['localhost', '127.0.0.1', '10.0.0.0/8']
    }
  },
  onExceedSoft: 'request-more'
};

// Resource Boundary: Intensive Task (e.g., video rendering)
const intensiveTaskBoundary: ResourceBoundary = {
  id: 'resource-intensive',
  type: 'resource',
  enforcement: 'soft',
  limits: {
    compute: {
      maxCpuTime: 600000,       // 10 minutes
      maxConcurrentExecutions: 5,
      priorityLevels: 10
    },
    memory: {
      maxHeapSize: 4 * 1024 * 1024 * 1024,  // 4 GB
      maxStackSize: 64 * 1024 * 1024,       // 64 MB
      maxTotalAllocation: 8 * 1024 * 1024 * 1024  // 8 GB
    },
    storage: {
      maxPersistentStorage: 10 * 1024 * 1024 * 1024,  // 10 GB
      maxTempStorage: 50 * 1024 * 1024 * 1024,        // 50 GB
      maxObjectSize: 5 * 1024 * 1024 * 1024           // 5 GB
    },
    network: {
      maxBandwidth: 10 * 1024 * 1024,  // 10 MB/s
      maxConnections: 50,
      allowedProtocols: ['https', 'wss', 's3'],
      blockedHosts: []
    }
  },
  onExceedSoft: 'throttle'
};
```

---

### 3. Semantic Boundaries (Soft)

**Purpose**: Ensure components are well-defined and composable.

```typescript
interface SemanticBoundary extends Boundary {
  type: 'semantic';
  enforcement: 'soft';  // Can be overridden with justification
  
  requirements: {
    // Interface requirements
    mustDeclareInputs: boolean;
    mustDeclareOutputs: boolean;
    mustDeclareSideEffects: boolean;
    inputOutputTypesRequired: boolean;
    
    // Documentation requirements
    descriptionRequired: boolean;
    minDescriptionLength: number;
    examplesRequired: boolean;
    
    // Composability requirements
    mustBeDeterministic: boolean;  // Same input → same output
    idempotencyPreferred: boolean;
    pureFunctionsPreferred: boolean;
    
    // Type system
    typeSystem: 'typescript' | 'json-schema' | 'custom';
    strictTypeChecking: boolean;
  };
}
```

**Examples**:

```typescript
// Semantic Boundary: Well-Documented Components
const documentationBoundary: SemanticBoundary = {
  id: 'semantic-documentation',
  type: 'semantic',
  enforcement: 'soft',
  requirements: {
    mustDeclareInputs: true,
    mustDeclareOutputs: true,
    mustDeclareSideEffects: true,
    inputOutputTypesRequired: true,
    descriptionRequired: true,
    minDescriptionLength: 50,
    examplesRequired: true,
    mustBeDeterministic: false,  // Allow non-deterministic (e.g., LLM calls)
    idempotencyPreferred: true,
    pureFunctionsPreferred: false,
    typeSystem: 'typescript',
    strictTypeChecking: true
  }
};

// Semantic Boundary: Strict Composability
const composabilityBoundary: SemanticBoundary = {
  id: 'semantic-composability',
  type: 'semantic',
  enforcement: 'soft',
  requirements: {
    mustDeclareInputs: true,
    mustDeclareOutputs: true,
    mustDeclareSideEffects: true,
    inputOutputTypesRequired: true,
    descriptionRequired: true,
    minDescriptionLength: 100,
    examplesRequired: true,
    mustBeDeterministic: true,
    idempotencyPreferred: true,
    pureFunctionsPreferred: true,
    typeSystem: 'typescript',
    strictTypeChecking: true
  }
};
```

---

### 4. User-Defined Boundaries (Hard/Soft)

**Purpose**: Allow users to customize constraints for their specific needs.

```typescript
interface UserDefinedBoundary extends Boundary {
  type: 'user_defined';
  enforcement: 'hard' | 'soft';
  
  // User can define any constraint
  constraints: UserConstraint[];
  
  // Who can modify this boundary
  modifiableBy: 'owner' | 'collaborators' | 'anyone';
  
  // When this boundary applies
  scope: 'project' | 'space' | 'component' | 'global';
}

type UserConstraint =
  | { type: 'no_cloud_apis'; message: string }
  | { type: 'only_local_execution'; message: string }
  | { type: 'max_cost_per_execution'; maxCost: number; currency: string }
  | { type: 'required_review'; reviewerRoles: string[] }
  | { type: 'allowed_data_sources'; sources: string[] }
  | { type: 'prohibited_topics'; topics: string[] }
  | { type: 'custom'; rule: string; validator: ValidatorFunction };
```

**Examples**:

```typescript
// User Boundary: Privacy-First Project
const privacyFirstBoundary: UserDefinedBoundary = {
  id: 'user-privacy-first',
  type: 'user_defined',
  enforcement: 'hard',
  constraints: [
    { type: 'no_cloud_apis', message: 'All processing must be local' },
    { type: 'only_local_execution', message: 'No external services' },
    { 
      type: 'custom', 
      rule: 'data_never_leaves_device',
      validator: (component) => !component.signature.sideEffects.includes('network')
    }
  ],
  modifiableBy: 'owner',
  scope: 'project'
};

// User Boundary: Budget-Conscious
const budgetBoundary: UserDefinedBoundary = {
  id: 'user-budget',
  type: 'user_defined',
  enforcement: 'soft',
  constraints: [
    { type: 'max_cost_per_execution', maxCost: 0.10, currency: 'USD' }
  ],
  modifiableBy: 'owner',
  scope: 'space'
};

// User Boundary: Educational Content Only
const educationalBoundary: UserDefinedBoundary = {
  id: 'user-educational',
  type: 'user_defined',
  enforcement: 'hard',
  constraints: [
    { type: 'prohibited_topics', topics: ['adult', 'gambling', 'weapons'] },
    { 
      type: 'custom',
      rule: 'educational_tone_required',
      validator: (component) => component.signature.metadata?.tone === 'educational'
    }
  ],
  modifiableBy: 'collaborators',
  scope: 'project'
};
```

---

## Boundary Composition

Multiple boundaries can be combined to create a complete constraint environment.

```typescript
interface BoundarySet {
  id: string;
  name: string;
  description: string;
  
  boundaries: {
    safety: SafetyBoundary[];
    resource: ResourceBoundary[];
    semantic: SemanticBoundary[];
    userDefined: UserDefinedBoundary[];
  };
  
  // How conflicts are resolved
  conflictResolution: 'most_restrictive' | 'least_restrictive' | 'priority_order';
  priorityOrder?: BoundaryId[];
}
```

**Example: Complete Boundary Set for a Book Writing Project**

```typescript
const bookWritingBoundarySet: BoundarySet = {
  id: 'book-writing-standard',
  name: 'Standard Book Writing Environment',
  description: 'Safe, resource-conscious environment for creative writing',
  
  boundaries: {
    safety: [
      noCodeExecBoundary,
      dataPrivacyBoundary,
      {
        id: 'safety-content-policy',
        type: 'safety',
        enforcement: 'hard',
        rules: {
          noHarmfulContentGeneration: true,
          contentFilterLevel: 'standard'
        }
      }
    ],
    
    resource: [
      standardProjectBoundary,
      {
        id: 'resource-llm-calls',
        type: 'resource',
        enforcement: 'soft',
        limits: {
          llm: {
            maxTokensPerExecution: 100000,
            maxConcurrentCalls: 5,
            preferredModels: ['claude-3-opus', 'claude-3-sonnet']
          }
        }
      }
    ],
    
    semantic: [
      documentationBoundary,
      {
        id: 'semantic-creative',
        type: 'semantic',
        enforcement: 'soft',
        requirements: {
          // Creative components can be non-deterministic
          mustBeDeterministic: false,
          // But should still be well-documented
          descriptionRequired: true,
          minDescriptionLength: 30
        }
      }
    ],
    
    userDefined: [
      {
        id: 'user-book-preferences',
        type: 'user_defined',
        enforcement: 'soft',
        constraints: [
          { type: 'allowed_data_sources', sources: ['local-files', 'user-provided-urls'] }
        ],
        modifiableBy: 'owner',
        scope: 'project'
      }
    ]
  },
  
  conflictResolution: 'most_restrictive'
};
```

---

## Boundary Enforcement Pipeline

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                      BOUNDARY ENFORCEMENT PIPELINE                           │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Input: Component Draft                                                      │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STAGE 1: Static Analysis                                            │    │
│  │                                                                      │    │
│  │  Parse component code/prompt                                         │    │
│  │  ↓                                                                   │    │
│  │  Check for prohibited patterns (safety)                              │    │
│  │  ↓                                                                   │
│  │  Validate type signatures (semantic)                                 │    │
│  │  ↓                                                                   │    │
│  │  Estimate resource requirements (resource)                           │    │
│  │                                                                      │    │
│  │  [PASS] ──► STAGE 2  /  [FAIL] ──► REJECT with explanation           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STAGE 2: Signature Validation                                       │    │
│  │                                                                      │    │
│  │  Verify all inputs declared                                          │    │
│  │  ↓                                                                   │    │
│  │  Verify all outputs declared                                         │    │
│  │  ↓                                                                   │    │
│  │  Verify side effects documented                                      │    │
│  │  ↓                                                                   │    │
│  │  Check description quality                                           │    │
│  │                                                                      │    │
│  │  [PASS] ──► STAGE 3  /  [WARN] ──► ACCEPT with suggestions           │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STAGE 3: Resource Allocation Check                                  │    │
│  │                                                                      │    │
│  │  Check if resources available                                        │    │
│  │  ↓                                                                   │    │
│  │  Reserve resources                                                   │    │
│  │  ↓                                                                   │    │
│  │  Set up monitoring                                                   │    │
│  │                                                                      │    │
│  │  [PASS] ──► STAGE 4  /  [QUEUE] ──► Wait for resources               │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  STAGE 4: User-Defined Constraint Check                              │    │
│  │                                                                      │    │
│  │  Apply custom validators                                             │    │
│  │  ↓                                                                   │    │
│  │  Check against user preferences                                      │    │
│  │  ↓                                                                   │    │
│  │  Verify project-specific rules                                       │    │
│  │                                                                      │    │
│  │  [PASS] ──► APPROVED  /  [SOFT FAIL] ──► REQUEST OVERRIDE            │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  Output: Approved Component + Resource Allocation                            │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Runtime Boundary Monitoring

Boundaries are not just checked at creation—they are monitored continuously.

```typescript
interface BoundaryMonitor {
  // Real-time monitoring
  watch(componentId: ComponentId): Watcher;
  
  // Violation detection
  detectViolations(): Violation[];
  
  // Response to violations
  handleViolation(violation: Violation): ViolationResponse;
}

interface Violation {
  id: string;
  boundaryId: BoundaryId;
  componentId: ComponentId;
  type: 'safety' | 'resource' | 'semantic' | 'user_defined';
  severity: 'critical' | 'high' | 'medium' | 'low';
  description: string;
  evidence: unknown;
  timestamp: Date;
}

type ViolationResponse =
  | { action: 'terminate'; reason: string }
  | { action: 'throttle'; factor: number; duration: number }
  | { action: 'warn'; message: string }
  | { action: 'request_approval'; from: 'user' | 'hey' }
  | { action: 'auto_correct'; correction: Correction };
```

**Monitoring Strategies**:

1. **Safety Monitoring**: Continuous, cannot be disabled
2. **Resource Monitoring**: Sampled every 100ms
3. **Semantic Monitoring**: Checked at interaction boundaries
4. **User-Defined Monitoring**: Configurable frequency

---

## Boundary Evolution

Boundaries themselves can evolve based on system learning.

```typescript
interface BoundaryEvolution {
  // Learn from violations
  learnFromViolation(violation: Violation): BoundaryUpdate;
  
  // Optimize boundaries for common patterns
  optimizeForPattern(pattern: EmergentPattern): BoundaryUpdate;
  
  // Suggest boundary relaxations for innovation
  suggestRelaxations(): BoundarySuggestion[];
}
```

**Example: Learning from Violations**

```typescript
// Initial boundary: max 10 concurrent components
const initialBoundary: ResourceBoundary = {
  limits: { compute: { maxConcurrentExecutions: 10 } }
};

// After observing system behavior:
// - 95% of tasks complete with < 5 concurrent components
// - Violations mostly occur during initialization
// - System is stable with up to 15 components

// Suggested evolution:
const evolvedBoundary: ResourceBoundary = {
  limits: { 
    compute: { 
      maxConcurrentExecutions: 15,
      // Dynamic limit based on system load
      adaptiveLimit: {
        base: 10,
        max: 20,
        scaleFactor: 'cpu_load_inverse'
      }
    } 
  }
};
```

---

## Boundary APIs

### For Agents

```typescript
interface AgentBoundaryAPI {
  // Query current boundaries
  getActiveBoundaries(): Boundary[];
  
  // Check if an action would violate boundaries
  wouldViolate(action: ProposedAction): ViolationCheck;
  
  // Request boundary relaxation (with justification)
  requestRelaxation(
    boundaryId: BoundaryId, 
    justification: string,
    duration?: number
  ): Promise<RelaxationResponse>;
  
  // Get boundary explanations
  explain(boundaryId: BoundaryId): BoundaryExplanation;
}
```

### For Users

```typescript
interface UserBoundaryAPI {
  // Create custom boundaries
  createBoundary(config: UserBoundaryConfig): UserDefinedBoundary;
  
  // Modify existing boundaries
  updateBoundary(id: BoundaryId, patch: BoundaryPatch): void;
  
  // View boundary violations
  getViolations(filter?: ViolationFilter): Violation[];
  
  // Override boundary (with audit trail)
  override(boundaryId: BoundaryId, reason: string, duration: number): void;
  
  // Get boundary recommendations
  getRecommendations(): BoundaryRecommendation[];
}
```

---

## Best Practices

### For Boundary Designers

1. **Start Restrictive, Relax Gradually**: Begin with tight boundaries and loosen based on observed needs
2. **Prefer Soft Boundaries**: Give agents room to innovate
3. **Make Boundaries Explainable**: Agents and users should understand why boundaries exist
4. **Monitor and Evolve**: Boundaries should improve over time
5. **Balance Safety and Freedom**: Too restrictive → no innovation; too loose → risk

### For Agent Developers

1. **Query Boundaries First**: Check what is allowed before proposing components
2. **Provide Justifications**: When requesting boundary relaxations, explain why
3. **Design for Constraints**: Embrace boundaries as creative constraints
4. **Handle Violations Gracefully**: Components should fail safely

---

## Summary

| Boundary Type | Enforcement | Purpose | Example |
|--------------|-------------|---------|---------|
| **Safety** | Hard | Prevent harm | No arbitrary code execution |
| **Resource** | Hard/Soft | Fair resource use | Max 512MB memory |
| **Semantic** | Soft | Ensure composability | Must declare inputs/outputs |
| **User-Defined** | Hard/Soft | Custom constraints | No cloud APIs |

**Key Insight**: Boundaries are not obstacles—they are the guardrails that enable freedom. Within well-defined boundaries, agents can create with confidence, knowing they won't break the system or violate user trust.

---

*Last Updated: 2026-05-20*
*Status: Boundary System Defined*
