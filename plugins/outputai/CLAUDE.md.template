<!-- output-agents-v0.0.1 -->
# Output.ai Based Project Guide

This file teaches agents how to work with the Output Framework - a deterministic workflow engine for building AI-powered automation.

## What is Output.ai?

Output.ai provides infrastructure for building production-grade AI workflows: fact checkers, content generators, data extractors, research assistants, and multi-step agents. Built on Temporal, it guarantees **durable execution** - if execution fails mid-run, it resumes from the last successful step.

## Core Philosophy

**Separation of orchestration from I/O:**
- **Workflows** orchestrate execution (must be deterministic - no I/O)
- **Steps/Evaluators** handle all I/O operations (HTTP, LLM, database calls)

This separation enables automatic retries, resumption, and debugging.

## Component Taxonomy

| Component | Purpose | Key Rule |
|-----------|---------|----------|
| **Workflow** | Orchestrates step execution | Must be deterministic (no I/O, no Date.now(), no Math.random()) |
| **Step** | Handles all I/O operations | Where HTTP, LLM, DB calls happen |
| **Evaluator** | Quality assessment | Returns confidence-scored results for validation loops |
| **Scenario** | Test input data | JSON files matching workflow's inputSchema |
| **Prompt** | LLM templates | Liquid.js templating with YAML frontmatter config |

## Project Structure

```
src/
├── shared/                      # Shared code across workflows
│   ├── clients/                 # API clients (e.g., jina.ts, stripe.ts)
│   └── utils/                   # Utility functions (e.g., string.ts)
└── workflows/                   # Workflow definitions
    └── {workflow_name}/
        ├── workflow.ts          # Orchestration logic (deterministic)
        ├── steps.ts             # I/O operations
        ├── types.ts             # Zod schemas (input, output, internal)
        ├── evaluators.ts        # Quality checks (optional)
        ├── utils.ts             # Local utilities (optional)
        ├── prompts/             # LLM templates (optional)
        │   └── generate@v1.prompt
        └── scenarios/           # Test inputs (optional)
            └── happy_path.json
```

## Code Reuse Rules

**Shared directory** (`src/shared/`):
- `shared/clients/` - API clients using `@output.ai/http` for external services
- `shared/utils/` - Helper functions and utilities

**Allowed imports:**
- Workflows/steps can import from `../../shared/clients/*.js` and `../../shared/utils/*.js`
- Workflows/steps can import from local files (`./types.js`, `./utils.js`)

**Forbidden:**
- Importing from sibling workflow folders (`../other_workflow/steps.js`)
- Steps importing other steps (activity isolation requirement)

## Critical Rules

| Rule | Correct | Incorrect |
|------|---------|-----------|
| Zod import | `import { z } from '@output.ai/core'` | `import { z } from 'zod'` |
| HTTP client | `import { http } from '@output.ai/http'` | `import axios from 'axios'` |
| LLM calls | `import { generateText } from '@output.ai/llm'` | Direct provider SDK |
| ES imports | `import { fn } from './file.js'` | `import { fn } from './file'` |
| Workflow I/O | Call steps for any I/O | Direct fetch/http in workflow |

**Determinism violations (never in workflows):**
- `Date.now()`, `new Date()`
- `Math.random()`, `crypto.randomUUID()`
- Direct HTTP/fetch calls
- File system operations
- Environment variable reads

## CLI Quick Reference

```bash
# Development
npx output dev                              # Start dev environment

# List & inspect
npx output workflow list                    # List available workflows

# Execute
npx output workflow run <name> --input '{}'  # Run synchronously (waits)
npx output workflow start <name> --input '{}' # Run async (returns ID)
npx output workflow status <id>              # Check async status
npx output workflow result <id>              # Get async result

# Debug
npx output workflow debug <id>               # Debug failed workflow
npx output workflow debug <id> --format json # Machine-readable output
```

## Available Commands

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/outputai:plan_workflow` | Plan workflow architecture | **ALWAYS FIRST** - creates implementation blueprint |
| `/outputai:build_workflow` | Build/implement workflows | After planning, or for modifications |
| `/outputai:debug_workflow` | Debug workflow issues | When workflows fail or behave unexpectedly |

## Skill Reference

### Development Skills
| Skill | Purpose |
|-------|---------|
| `output-dev-folder-structure` | Project and workflow directory layout |
| `output-dev-workflow-function` | Writing deterministic workflow files |
| `output-dev-step-function` | Writing step functions for I/O |
| `output-dev-types-file` | Zod schema definitions |
| `output-dev-evaluator-function` | Quality assessment functions |
| `output-dev-prompt-file` | LLM prompt templates with Liquid.js |
| `output-dev-scenario-file` | Test input JSON files |
| `output-dev-http-client-create` | Shared HTTP API client patterns |
| `output-dev-create-skeleton` | Generate workflow skeleton |

### Error Pattern Skills
| Skill | Catches |
|-------|---------|
| `output-error-zod-import` | Wrong zod import source |
| `output-error-nondeterminism` | Date.now, Math.random in workflows |
| `output-error-try-catch` | Missing error handling in steps |
| `output-error-missing-schemas` | Incomplete Zod schema exports |
| `output-error-direct-io` | I/O operations in workflow files |
| `output-error-http-client` | Using axios instead of @output.ai/http |

### CLI Operation Skills
| Skill | Purpose |
|-------|---------|
| `output-workflow-run` | Synchronous workflow execution |
| `output-workflow-start` | Async workflow execution |
| `output-workflow-status` | Check workflow status |
| `output-workflow-result` | Get workflow result |
| `output-workflow-list` | List available workflows |
| `output-workflow-runs-list` | List workflow run history |
| `output-workflow-debug` | Debug failed workflows |
| `output-workflow-trace` | Trace workflow execution |
| `output-workflow-stop` | Stop running workflow |
| `output-services-check` | Verify Output services status |

### Meta Skills
| Skill | Purpose |
|-------|---------|
| `output-meta-pre-flight` | Pre-operation validation checks |
| `output-meta-post-flight` | Post-operation verification |
| `output-meta-project-context` | Gather project context |

## Practical Tips

### Docker & Services
- **Restart worker after adding workflows**: `docker restart <project>-worker-1`
- **View worker logs**: `docker logs -f output-worker-1`
- **Check services**: Use `output-services-check` skill

### Payload Limits
- Temporal: ~2MB per workflow input/output
- gRPC: ~4MB maximum
- For larger data, use file storage and pass references

### Debugging Workflow Failures
1. Get the workflow ID from error output
2. Run `npx output workflow debug <id> --format json`
3. Look for: failed step name, error message, input that caused failure
4. Check if issue is determinism, schema validation, or external API

### Naming Conventions

| Element | Convention | Example |
|---------|------------|---------|
| Workflow folder | snake_case | `fact_checker/` |
| Workflow name | snake_case | `name: 'fact_checker'` |
| Step functions | camelCase | `fetchArticle()`, `analyzeContent()` |
| Schema names | PascalCase | `InputSchema`, `ArticleData` |
| Prompt files | snake_case.prompt | `analyze_claim.prompt` |
| Scenario files | snake_case.json | `happy_path.json` |

## Common Patterns

### Workflow
```typescript
import { workflow, z } from '@output.ai/core';
import { fetchData, processData } from './steps.js';

export const inputSchema = z.object({ url: z.string().url() });
export const outputSchema = z.object({ result: z.string() });

export default workflow({
  name: 'my_workflow',
  description: 'Processes data from URL',
  inputSchema,
  outputSchema,
  fn: async (input) => {
    const data = await fetchData(input.url);
    const result = await processData(data);
    return { result };
  }
});
```

See `output-dev-workflow-function` for comprehensive patterns.

### Step
```typescript
import { step, z } from '@output.ai/core';
import { httpClient } from '@output.ai/http';

export const fetchData = step(
  { name: 'fetchData', inputSchema: z.string(), outputSchema: z.any() },
  async (url) => {
    const client = httpClient({ prefixUrl: url });
    const response = await client.get('');
    return response.json();
  }
);
```

See `output-dev-step-function` for comprehensive patterns.

### HTTP Client (Shared)

Clients live in `src/shared/clients/` and are shared across all workflows.

```typescript
// src/shared/clients/example.ts
import { FatalError, ValidationError } from '@output.ai/core';
import { httpClient } from '@output.ai/http';

const API_KEY = process.env.EXAMPLE_API_KEY || '';

const client = httpClient({
  prefixUrl: 'https://api.example.com',
  headers: { Authorization: `Bearer ${API_KEY}` },
  timeout: 30000,
  retry: { limit: 3, statusCodes: [408, 429, 500, 502, 503, 504] }
});

export async function fetchFromExample(query: string): Promise<ExampleResponse> {
  if (!API_KEY) throw new FatalError('EXAMPLE_API_KEY not set');

  try {
    const response = await client.get('endpoint', { searchParams: { q: query } });
    return response.json();
  } catch (error: unknown) {
    const err = error as { status?: number; message?: string };
    if (err.status === 401 || err.status === 403) {
      throw new FatalError(`Auth failed: ${err.message}`);
    }
    throw new ValidationError(`Request failed: ${err.message}`);
  }
}
```

**Error type guidelines:**
- `FatalError`: 401, 403, 404 (won't succeed on retry)
- `ValidationError`: 429, 5xx (may succeed on retry)

See `output-dev-http-client-create` for comprehensive patterns.

### Evaluator

Evaluators return confidence-scored results. Three result types available:

```typescript
// src/workflows/my_workflow/evaluators.ts
import { evaluator, z, EvaluationBooleanResult, EvaluationNumberResult, EvaluationStringResult } from '@output.ai/core';

// Boolean evaluator - pass/fail checks
export const evaluateCompleteness = evaluator({
  name: 'evaluate_completeness',
  description: 'Check if content meets minimum length',
  inputSchema: z.object({ content: z.string(), minLength: z.number() }),
  fn: async ({ content, minLength }) => {
    return new EvaluationBooleanResult({
      value: content.length >= minLength,
      confidence: 1.0,
      reasoning: `Content has ${content.length} chars (min: ${minLength})`
    });
  }
});

// Number evaluator - scoring
export const evaluateQuality = evaluator({
  name: 'evaluate_quality',
  description: 'Score content quality 0-100',
  inputSchema: z.object({ content: z.string() }),
  fn: async ({ content }) => {
    const score = calculateScore(content); // your logic
    return new EvaluationNumberResult({
      value: score,
      confidence: 0.85
    });
  }
});

// String evaluator - classification
export const evaluateSentiment = evaluator({
  name: 'evaluate_sentiment',
  description: 'Classify sentiment',
  inputSchema: z.object({ content: z.string() }),
  fn: async ({ content }) => {
    return new EvaluationStringResult({
      value: 'positive', // or 'negative', 'neutral'
      confidence: 0.9
    });
  }
});
```

**LLM-powered evaluator:**
```typescript
import { evaluator, z, EvaluationNumberResult } from '@output.ai/core';
import { generateObject } from '@output.ai/llm';

export const evaluateWithLLM = evaluator({
  name: 'evaluate_with_llm',
  description: 'LLM-based quality assessment',
  inputSchema: z.object({ content: z.string() }),
  fn: async ({ content }) => {
    const { result } = await generateObject({
      prompt: 'quality_check@v1',  // References prompts/quality_check@v1.prompt
      variables: { content },
      schema: z.object({ score: z.number().min(0).max(100) })
    });
    return new EvaluationNumberResult({ value: result.score, confidence: 0.85 });
  }
});
```

See `output-dev-evaluator-function` for comprehensive patterns.

### Prompt File

Prompts use YAML frontmatter + Liquid.js templating. Location: `src/workflows/{name}/prompts/`

```
// prompts/analyze@v1.prompt
---
provider: anthropic
model: claude-sonnet-4
temperature: 0.7
maxTokens: 4096
---

<system>
You are an expert content analyzer.

{% if context %}
Additional context: {{ context }}
{% endif %}
</system>

<user>
Analyze the following content:

<content>
{{ content }}
</content>

Provide {{ numberOfPoints | default: 3 }} key insights.
</user>
```

**Using in steps:**
```typescript
import { generateObject, generateText } from '@output.ai/llm';
import { z } from '@output.ai/core';

// Structured output
const { result } = await generateObject({
  prompt: 'analyze@v1',
  variables: { content: 'Article text...', numberOfPoints: 5 },
  schema: z.object({ insights: z.array(z.string()) })
});

// Text output
const { result } = await generateText({
  prompt: 'summarize@v1',
  variables: { content: 'Article text...' }
});
```

**Provider options:**
| Provider | Model Examples |
|----------|----------------|
| `anthropic` | `claude-sonnet-4`, `claude-opus-4` |
| `openai` | `gpt-4o`, `gpt-4o-mini` |
| `vertex` | `gemini-2.0-flash`, `gemini-2.5-pro` |

See `output-dev-prompt-file` for comprehensive patterns.
