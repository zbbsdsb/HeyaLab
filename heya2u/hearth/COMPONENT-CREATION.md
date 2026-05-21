# Component Creation

> How agents invent components within boundaries.

---

## The Invention Process

In Hearth, components are not selected from a library—they are invented on demand by agents.

```
Traditional Component System:          Hearth Component Invention:
┌──────────────────────────┐          ┌──────────────────────────┐
│  1. User needs feature   │          │  1. User expresses intent│
│  2. Search component lib │          │  2. Agent analyzes need  │
│  3. Find closest match   │          │  3. Designs signature    │
│  4. Adapt/configure      │          │  4. Generates implementation
│  5. Use component        │          │  5. Validates & registers│
│                          │          │  6. Component is born    │
│  Limited to what exists  │          │  Infinite possibilities  │
└──────────────────────────┘          └──────────────────────────┘
```

---

## The Agent as Inventor

### Agent Capabilities

```typescript
interface ComponentInventor {
  // Analyze what component is needed
  analyzeNeed(intent: UserIntent, context: Context): ComponentRequirement;
  
  // Design the component's interface
  designSignature(requirement: ComponentRequirement): ComponentSignature;
  
  // Generate implementation
  generateImplementation(signature: ComponentSignature): Implementation;
  
  // Validate against boundaries
  validate(component: ComponentDraft): ValidationResult;
  
  // Register the component
  register(component: Component): RegistrationResult;
}
```

### Invention Workflow

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                    COMPONENT INVENTION WORKFLOW                              │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  TRIGGER: User Intent or System Need                                         │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  PHASE 1: NEED ANALYSIS                                              │    │
│  │                                                                      │    │
│  │  Input: "Write a book about dragons"                                 │    │
│  │  ↓                                                                   │    │
│  │  Parse intent:                                                       │    │
│  │    - Action: write                                                   │    │
│  │    - Object: book                                                    │    │
│  │    - Subject: dragons                                                │    │
│  │    - Implicit: needs plot, characters, chapters, world-building      │    │
│  │  ↓                                                                   │    │
│  │  Identify capability gaps:                                           │    │
│  │    - No "dragon book writer" component exists                        │    │
│  │    - Need: PlotGenerator, CharacterDesigner, WorldBuilder            │    │
│  │    - Need: ChapterPlanner, StyleAnalyzer                             │    │
│  │                                                                      │    │
│  │  Output: ComponentRequirements[]                                     │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  PHASE 2: SIGNATURE DESIGN                                           │    │
│  │                                                                      │    │
│  │  For each requirement, design:                                       │    │
│  │                                                                      │    │
│  │  Component: PlotGenerator                                            │    │
│  │  ├── Name: PlotGenerator                                             │    │
│  │  ├── Description: Generates story plots based on genre, themes,      │    │
│  │  │               and character profiles                               │    │
│  │  ├── Inputs:                                                         │    │
│  │  │   ├── genre: string ("fantasy", "sci-fi", etc.)                   │    │
│  │  │   ├── themes: string[]                                           │    │
│  │  │   ├── characters: CharacterProfile[]                             │    │
│  │  │   └── setting: WorldDescription                                  │    │
│  │  ├── Outputs:                                                        │    │
│  │  │   ├── plot: PlotStructure                                        │    │
│  │  │   ├── acts: Act[]                                                │    │
│  │  │   └── keyScenes: Scene[]                                         │    │
│  │  ├── SideEffects:                                                    │    │
│  │  │   ├── writes to state.graph.plot                                 │    │
│  │  │   └── may call LLM for generation                                │    │
│  │  └── Examples:                                                       │    │
│  │      ├── Example 1: Fantasy with redemption theme                   │    │
│  │      └── Example 2: Sci-fi with AI ethics theme                     │    │
│  │                                                                      │    │
│  │  Output: ComponentSignature                                          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  PHASE 3: IMPLEMENTATION GENERATION                                  │    │
│  │                                                                      │    │
│  │  Choose implementation strategy:                                     │    │
│  │                                                                      │    │
│  │  Option A: Code-Based                                                │    │
│  │  ```typescript                                                       │    │
│  │  const PlotGenerator: Component = {                                  │    │
│  │    signature: { /* ... */ },                                         │    │
│  │    implementation: {                                                 │    │
│  │      type: 'code',                                                   │    │
│  │      language: 'typescript',                                         │    │
│  │      code: `                                                         │    │
│  │        async function execute(inputs) {                              │    │
│  │          const { genre, themes, characters, setting } = inputs;      │    │
│  │          // Algorithmic plot generation                              │    │
│  │          const plot = await generatePlotStructure(...);              │    │
│  │          return { plot, acts: plot.acts, keyScenes: plot.scenes };   │    │
│  │        }                                                             │    │
│  │      `                                                               │    │
│  │    }                                                                 │    │
│  │  };                                                                  │    │
│  │  ```                                                                 │    │
│  │                                                                      │    │
│  │  Option B: Prompt-Based (LLM)                                        │    │
│  │  ```typescript                                                       │    │
│  │  const PlotGenerator: Component = {                                  │    │
│  │    signature: { /* ... */ },                                         │    │
│  │    implementation: {                                                 │    │
│  │      type: 'prompt',                                                 │    │
│  │      template: `                                                     │    │
│  │        You are an expert plot designer.                              │    │
│  │        Genre: {{genre}}                                              │    │
│  │        Themes: {{themes}}                                            │    │
│  │        Characters: {{characters}}                                    │    │
│  │        Setting: {{setting}}                                          │    │
│  │                                                                      │    │
│  │        Design a compelling plot structure with:                      │    │
│  │        - 3-act structure                                             │    │
│  │        - Character arcs                                              │    │
│  │        - 5-7 key scenes                                              │    │
│  │      `,                                                              │    │
│  │      model: 'claude-3-opus',                                         │    │
│  │      temperature: 0.7                                                │    │
│  │    }                                                                 │    │
│  │  };                                                                  │    │
│  │  ```                                                                 │    │
│  │                                                                      │    │
│  │  Option C: Hybrid                                                    │    │
│  │  ```typescript                                                       │    │
│  │  const PlotGenerator: Component = {                                  │    │
│  │    signature: { /* ... */ },                                         │    │
│  │    implementation: {                                                 │    │
│  │      type: 'hybrid',                                                 │    │
│  │      steps: [                                                        │    │
│  │        { type: 'code', fn: 'validateInputs' },                       │    │
│  │        { type: 'prompt', template: 'generateOutline' },              │    │
│  │        { type: 'code', fn: 'structureOutput' },                      │    │
│  │        { type: 'prompt', template: 'refineScenes' }                  │    │
│  │      ]                                                               │    │
│  │    }                                                                 │    │
│  │  };                                                                  │    │
│  │  ```                                                                 │    │
│  │                                                                      │    │
│  │  Output: Implementation                                              │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  PHASE 4: BOUNDARY VALIDATION                                        │    │
│  │                                                                      │    │
│  │  Check against all active boundaries:                                │    │
│  │                                                                      │    │
│  │  Safety:    ✓ No arbitrary code execution                            │    │
│  │  Resource:  ✓ Memory usage within limits                               │    │
│  │  Semantic:  ✓ All inputs/outputs declared                              │    │
│  │  User:      ✓ No cloud API usage (user preference)                     │    │
│  │                                                                      │    │
│  │  [PASS] ──► PHASE 5  /  [FAIL] ──► REVISE or ABANDON                   │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│      │                                                                       │
│      ▼                                                                       │
│  ┌─────────────────────────────────────────────────────────────────────┐    │
│  │  PHASE 5: REGISTRATION                                               │    │
│  │                                                                      │    │
│  │  1. Assign unique ID                                                 │    │
│  │  2. Set version (v1.0.0)                                             │    │
│  │  3. Initialize fitness tracking                                      │    │
│  │  4. Create state graph nodes                                         │    │
│  │  5. Announce to ecosystem                                            │    │
│  │                                                                      │    │
│  │  Output: Registered Component ready for use                          │    │
│  └─────────────────────────────────────────────────────────────────────┘    │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Implementation Strategies

### 1. Code-Based Implementation

For deterministic, algorithmic components.

```typescript
interface CodeImplementation {
  type: 'code';
  language: 'typescript' | 'javascript' | 'python' | 'wasm';
  code: string;
  dependencies: Dependency[];
  
  // Execution context
  sandbox: {
    timeout: number;
    memoryLimit: number;
    allowedGlobals: string[];
  };
}

// Example: Character Consistency Checker
const characterConsistencyChecker: CodeImplementation = {
  type: 'code',
  language: 'typescript',
  code: `
    async function execute(inputs: { 
      character: CharacterProfile, 
      proposedAction: string,
      storyContext: StoryContext 
    }) {
      const { character, proposedAction, storyContext } = inputs;
      
      // Check if action aligns with character traits
      const traits = character.traits;
      const alignment = calculateAlignment(traits, proposedAction);
      
      // Check against previous actions for consistency
      const previousActions = storyContext.getCharacterActions(character.id);
      const consistency = checkConsistency(previousActions, proposedAction);
      
      return {
        isConsistent: alignment > 0.7 && consistency > 0.8,
        alignment,
        consistency,
        suggestions: generateSuggestions(character, proposedAction)
      };
    }
  `,
  dependencies: [
    { name: 'alignment-calculator', version: '^1.0.0' }
  ],
  sandbox: {
    timeout: 5000,
    memoryLimit: 128 * 1024 * 1024,  // 128 MB
    allowedGlobals: ['console', 'Math', 'JSON']
  }
};
```

**When to use**:
- Data transformation
- Validation logic
- Algorithmic processing
- Deterministic operations

---

### 2. Prompt-Based Implementation

For creative, LLM-powered components.

```typescript
interface PromptImplementation {
  type: 'prompt';
  template: string;           // Template with {{variable}} placeholders
  model: string;              // LLM model to use
  temperature: number;        // Creativity (0.0 - 1.0)
  maxTokens: number;
  
  // Structured output
  outputSchema: JSONSchema;   // Expected output structure
  validation: {
    retryOnInvalid: boolean;
    maxRetries: number;
    fallbackStrategy: 'simplify' | 'alternative_prompt' | 'error';
  };
}

// Example: Dialogue Generator
const dialogueGenerator: PromptImplementation = {
  type: 'prompt',
  template: `
You are writing dialogue for a {{genre}} story.

Characters:
{{#each characters}}
- {{name}}: {{personality}}, speaks {{speechPattern}}
{{/each}}

Scene Context:
{{sceneContext}}

Emotional Tone: {{tone}}

Write natural dialogue (200-300 words) that:
1. Reveals character through speech patterns
2. Advances the plot
3. Maintains {{tone}} tone
4. Includes subtext and tension

Output as JSON:
{
  "dialogue": [
    {"speaker": "name", "line": "text", "emotion": "emotion", "subtext": "subtext"}
  ],
  "stageDirections": ["direction1", "direction2"],
  "plotAdvancement": "how this moves the story forward"
}
  `,
  model: 'claude-3-opus',
  temperature: 0.8,
  maxTokens: 2000,
  outputSchema: {
    type: 'object',
    properties: {
      dialogue: {
        type: 'array',
        items: {
          type: 'object',
          properties: {
            speaker: { type: 'string' },
            line: { type: 'string' },
            emotion: { type: 'string' },
            subtext: { type: 'string' }
          },
          required: ['speaker', 'line']
        }
      }
    },
    required: ['dialogue']
  },
  validation: {
    retryOnInvalid: true,
    maxRetries: 3,
    fallbackStrategy: 'simplify'
  }
};
```

**When to use**:
- Creative generation
- Natural language tasks
- Subjective evaluation
- Content creation

---

### 3. Hybrid Implementation

Combining code and prompts for complex workflows.

```typescript
interface HybridImplementation {
  type: 'hybrid';
  steps: HybridStep[];
  
  // State management between steps
  state: {
    type: 'shared' | 'isolated';
    persistence: 'none' | 'step' | 'full';
  };
  
  // Error handling
  onError: 'stop' | 'skip' | 'retry' | 'fallback';
}

type HybridStep = 
  | { type: 'code'; name: string; fn: string; }
  | { type: 'prompt'; name: string; template: string; model: string; }
  | { type: 'component'; name: string; componentId: string; }
  | { type: 'condition'; name: string; check: string; then: string; else: string; }
  | { type: 'parallel'; name: string; branches: HybridStep[][]; };

// Example: Chapter Writer
const chapterWriter: HybridImplementation = {
  type: 'hybrid',
  steps: [
    {
      type: 'code',
      name: 'validateInputs',
      fn: 'validateChapterInputs'
    },
    {
      type: 'prompt',
      name: 'generateOutline',
      template: 'Create detailed chapter outline for {{chapterNumber}}...',
      model: 'claude-3-opus'
    },
    {
      type: 'condition',
      name: 'checkOutlineQuality',
      check: 'outline.quality > 0.8',
      then: 'proceedToWriting',
      else: 'reviseOutline'
    },
    {
      type: 'parallel',
      name: 'writeSections',
      branches: [
        [
          { type: 'prompt', name: 'writeOpening', template: '...', model: 'claude-3-opus' },
          { type: 'code', name: 'validateOpening', fn: 'validateSection' }
        ],
        [
          { type: 'prompt', name: 'writeMiddle', template: '...', model: 'claude-3-opus' },
          { type: 'code', name: 'validateMiddle', fn: 'validateSection' }
        ],
        [
          { type: 'prompt', name: 'writeEnding', template: '...', model: 'claude-3-opus' },
          { type: 'code', name: 'validateEnding', fn: 'validateSection' }
        ]
      ]
    },
    {
      type: 'code',
      name: 'assembleChapter',
      fn: 'combineSections'
    },
    {
      type: 'component',
      name: 'consistencyCheck',
      componentId: 'character-consistency-checker'
    },
    {
      type: 'prompt',
      name: 'finalPolish',
      template: 'Polish this chapter for flow and style...',
      model: 'claude-3-sonnet'
    }
  ],
  state: {
    type: 'shared',
    persistence: 'full'
  },
  onError: 'retry'
};
```

**When to use**:
- Complex multi-step tasks
- When validation is needed between generation steps
- Combining multiple capabilities
- Workflows with branching/parallelism

---

## Component Signature Design

A well-designed signature is crucial for component composability.

```typescript
interface ComponentSignature {
  // Identity
  name: string;
  description: string;
  category: string;
  tags: string[];
  
  // Interface
  inputs: InputDefinition[];
  outputs: OutputDefinition[];
  sideEffects: SideEffectDefinition[];
  
  // Metadata
  examples: Example[];
  constraints: Constraint[];
  
  // Evolution
  version: string;
  parent?: string;  // Previous version
  lineage: string[];
}

interface InputDefinition {
  name: string;
  type: Type;
  description: string;
  required: boolean;
  default?: unknown;
  constraints?: Constraint[];
}

interface OutputDefinition {
  name: string;
  type: Type;
  description: string;
  optional: boolean;
}

interface SideEffectDefinition {
  type: 'read' | 'write' | 'network' | 'file' | 'external';
  target: string;
  description: string;
}
```

### Signature Design Principles

1. **Single Responsibility**: Each component does one thing well
2. **Clear Contracts**: Inputs and outputs are explicit
3. **Side Effect Transparency**: All side effects are declared
4. **Type Safety**: Strong typing for composability
5. **Self-Documenting**: Description explains purpose and usage

---

## Component Templates

Agents can use templates to accelerate invention.

```typescript
interface ComponentTemplate {
  id: string;
  name: string;
  description: string;
  
  // Template structure
  signature: Partial<ComponentSignature>;
  implementation: Partial<Implementation>;
  
  // Customization points
  parameters: TemplateParameter[];
  
  // Usage
  usageCount: number;
  averageFitness: number;
}

// Example Templates
const templates: ComponentTemplate[] = [
  {
    id: 'transformer-template',
    name: 'Data Transformer',
    description: 'Transforms input data from one format to another',
    signature: {
      inputs: [
        { name: 'input', type: 'any', description: 'Data to transform', required: true }
      ],
      outputs: [
        { name: 'output', type: 'any', description: 'Transformed data', optional: false }
      ],
      sideEffects: []
    },
    implementation: {
      type: 'code',
      language: 'typescript'
    },
    parameters: [
      { name: 'transformation', type: 'string', description: 'Describe the transformation' }
    ]
  },
  
  {
    id: 'generator-template',
    name: 'Content Generator',
    description: 'Generates content using LLM',
    signature: {
      inputs: [
        { name: 'prompt', type: 'string', description: 'Generation prompt', required: true },
        { name: 'context', type: 'object', description: 'Additional context', required: false }
      ],
      outputs: [
        { name: 'content', type: 'string', description: 'Generated content', optional: false },
        { name: 'metadata', type: 'object', description: 'Generation metadata', optional: true }
      ],
      sideEffects: [
        { type: 'external', target: 'llm-api', description: 'Calls LLM for generation' }
      ]
    },
    implementation: {
      type: 'prompt',
      model: 'claude-3-opus'
    },
    parameters: [
      { name: 'contentType', type: 'string', description: 'Type of content to generate' },
      { name: 'style', type: 'string', description: 'Desired style/tone' }
    ]
  },
  
  {
    id: 'validator-template',
    name: 'Content Validator',
    description: 'Validates content against criteria',
    signature: {
      inputs: [
        { name: 'content', type: 'any', description: 'Content to validate', required: true }
      ],
      outputs: [
        { name: 'isValid', type: 'boolean', description: 'Validation result', optional: false },
        { name: 'issues', type: 'array', description: 'List of issues found', optional: false },
        { name: 'suggestions', type: 'array', description: 'Improvement suggestions', optional: true }
      ],
      sideEffects: []
    },
    implementation: {
      type: 'hybrid'
    },
    parameters: [
      { name: 'validationCriteria', type: 'array', description: 'Criteria to validate against' }
    ]
  }
];
```

---

## Invention Patterns

### Pattern 1: Decomposition

Break complex needs into atomic components.

```
Need: "Write a book"
  ↓
Decompose:
  - PlotGenerator
  - CharacterDesigner
  - WorldBuilder
  - ChapterPlanner
  - SceneWriter
  - DialogueGenerator
  - ConsistencyChecker
  - StyleAnalyzer
```

### Pattern 2: Specialization

Create variants for specific contexts.

```
Base: DialogueGenerator
  ↓
Specialize:
  - FantasyDialogueGenerator
  - SciFiDialogueGenerator
  - RomanceDialogueGenerator
  - MysteryDialogueGenerator
```

### Pattern 3: Composition

Combine existing components into new capabilities.

```
Components:
  - PlotGenerator
  - CharacterDesigner
  
Compose:
  - CharacterDrivenPlotGenerator
    (uses CharacterDesigner to inform PlotGenerator)
```

### Pattern 4: Adaptation

Modify existing components for new needs.

```
Existing: ChapterPlanner (for novels)
  ↓
Adapt:
  - EpisodePlanner (for TV series)
  - LevelPlanner (for games)
  - SectionPlanner (for academic papers)
```

---

## Validation & Quality Assurance

### Static Validation

```typescript
interface StaticValidator {
  // Code analysis
  analyzeCode(code: string): CodeAnalysis;
  
  // Signature completeness
  validateSignature(signature: ComponentSignature): ValidationResult;
  
  // Boundary compliance
  checkBoundaries(component: ComponentDraft): BoundaryCheck;
  
  // Security scan
  securityScan(code: string): SecurityReport;
}
```

### Dynamic Validation

```typescript
interface DynamicValidator {
  // Test with sample inputs
  testComponent(component: Component, testCases: TestCase[]): TestResult;
  
  // Measure performance
  benchmark(component: Component, inputs: unknown[]): BenchmarkResult;
  
  // Check determinism
  checkDeterminism(component: Component, inputs: unknown[]): boolean;
}
```

### Quality Gates

```
┌─────────────────────────────────────────────────────────────────────────────┐
│                         QUALITY GATES                                       │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                              │
│  Gate 1: Syntax Validity                                                     │
│  ├── Code compiles without errors                                            │
│  └── Prompt template is valid                                                │
│                                                                              │
│  Gate 2: Signature Completeness                                              │
│  ├── All inputs have types and descriptions                                  │
│  ├── All outputs are declared                                                │
│  ├── Side effects are documented                                             │
│  └── Examples are provided                                                   │
│                                                                              │
│  Gate 3: Boundary Compliance                                                 │
│  ├── No safety violations                                                    │
│  ├── Resource usage within limits                                            │
│  └── User constraints satisfied                                              │
│                                                                              │
│  Gate 4: Functional Correctness                                              │
│  ├── Test cases pass                                                         │
│  ├── No runtime errors on valid inputs                                       │
│  └── Graceful handling of invalid inputs                                     │
│                                                                              │
│  Gate 5: Performance                                                         │
│  ├── Execution time within limits                                            │
│  ├── Memory usage within limits                                              │
│  └── Scales appropriately with input size                                    │
│                                                                              │
│  Gate 6: Composability                                                       │
│  ├── Can be called by other components                                       │
│  ├── Output types match expected inputs of related components                │
│  └── No hidden dependencies                                                  │
│                                                                              │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Best Practices

### For Component Inventors (Agents)

1. **Start Simple**: Create minimal viable components first
2. **Test Early**: Validate with simple inputs before complex scenarios
3. **Document Thoroughly**: Clear descriptions enable better composition
4. **Declare Everything**: Don't hide side effects or dependencies
5. **Design for Failure**: Components should fail gracefully
6. **Evolve Incrementally**: Improve components through mutation, not replacement

### For Component Users

1. **Check Signatures**: Understand inputs, outputs, and side effects
2. **Provide Context**: More context leads to better results
3. **Chain Thoughtfully**: Consider data flow between components
4. **Give Feedback**: User feedback drives component evolution
5. **Reuse When Possible**: Prefer existing components over new inventions

---

## Summary

Component creation in Hearth is:

| Aspect | Traditional | Hearth |
|--------|-------------|--------|
| **Source** | Pre-built library | Agent invention |
| **Timing** | Before need | On demand |
| **Customization** | Configuration | Full implementation control |
| **Scope** | Fixed capabilities | Infinite possibilities |
| **Evolution** | Version updates | Continuous improvement |
| **Discovery** | Search library | Emergent from interactions |

**Key Insight**: The agent is not just a user of components—it is their inventor. This shifts the paradigm from "what components exist?" to "what component do I need to invent?"

---

*Last Updated: 2026-05-20*
*Status: Component Creation System Defined*
