# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

This repository contains Claude Code plugins for Output.ai workflow development. It includes two main plugins:
- **outputai**: Workflow development tools for planning, building, and debugging Output SDK workflows
- **outputai-flow-migrator**: Migration tools for converting Flow SDK workflows to Output SDK

## Repository Structure

```
output-claude-plugins/
├── plugins/
│   ├── outputai/                    # Main workflow development plugin
│   │   ├── .claude-plugin/          # Plugin manifest
│   │   ├── agents/                  # Subagent definitions (workflow_planner, workflow_debugger, etc.)
│   │   ├── commands/                # Slash commands (build_workflow, debug_workflow, plan_workflow)
│   │   └── skills/                  # Reusable skills for workflow operations
│   └── outputai-flow-migrator/      # Flow SDK migration plugin
│       ├── .claude-plugin/          # Plugin manifest
│       ├── agents/                  # Migration-specific agents
│       ├── commands/                # Migration commands
│       └── skills/                  # Migration skills (conversion, analysis, validation)
└── .claude-plugin/                  # Marketplace configuration
    └── marketplace.json             # Plugin registry for distribution
```

## Plugin Architecture

This repository implements a **hierarchical AI-assisted development framework** with three layers:

### Commands (User-Facing)
Commands are slash commands that orchestrate complex workflows. They are markdown files with YAML frontmatter and XML process flows.

**Structure:**
- YAML frontmatter: `argument-hint`, `description`, `version`, `model`
- XML process flow: Numbered `<step>` elements with optional `subagent=""` attributes
- Template variables: `$ARGUMENTS`, `$1`, `$2`, etc.

**Key Pattern:** Commands use pre-flight validation → context gathering → main work → post-flight verification

**Available Commands:**
- `plan_workflow` - Generate comprehensive workflow implementation plan
- `build_workflow` - Implement workflow from plan document
- `debug_workflow` - Debug workflow execution issues

### Agents (Specialized Subagents)
Agents are specialized assistants invoked by commands via `subagent=""` XML attributes. Each has focused expertise and specific tool access.

**outputai Agents:**
- `workflow-planner` (Opus, Blue) - Designs workflow architecture and implementation blueprints
- `workflow-debugger` (Opus, Yellow) - Analyzes execution traces and identifies root causes
- `workflow-quality` (Sonnet, Green) - Reviews code quality and validates SDK conventions
- `workflow-prompt-writer` (Sonnet, Yellow) - Creates and optimizes LLM prompt files
- `workflow-context-fetcher` (Haiku, Blue) - Efficient documentation and pattern retrieval

**flow-migrator Agent:**
- `flow-workflow-migrator` (Opus, Purple) - Converts Flow SDK workflows to Output SDK

**Agent Structure:**
- YAML frontmatter: `name`, `description`, `tools`, `model`, `color`
- Markdown content: Identity, core mission, expertise domains, usage patterns

### Skills (Knowledge Modules)
Skills are reusable guidance modules automatically invoked by agents when context matches.

**Skill Categories:**

**Development Skills** (8 skills):
- `output-dev-folder-structure` - Directory layout planning
- `output-dev-create-skeleton` - Initial file structure generation
- `output-dev-workflow-function` - workflow.ts patterns
- `output-dev-step-function` - steps.ts implementation
- `output-dev-types-file` - Zod schema definitions
- `output-dev-http-client-create` - Shared HTTP client setup
- `output-dev-prompt-file` - .prompt file creation
- `output-dev-scenario-file` - Test scenario documentation

**Error Resolution Skills** (6 skills):
- `output-error-zod-import` - Fix "incompatible schema" errors
- `output-error-direct-io` - Fix workflow determinism violations
- `output-error-try-catch` - Remove error handling antipatterns
- `output-error-nondeterminism` - Fix random/Date non-determinism
- `output-error-http-client` - Fix HTTP request patterns
- `output-error-missing-schemas` - Fix schema validation failures

**Meta/Operational Skills** (3 skills):
- `output-meta-pre-flight` - **REQUIRED** pre-execution validation
- `output-meta-post-flight` - Post-execution verification
- `output-meta-project-context` - Output SDK knowledge base

**Workflow Operation Skills** (8 skills):
- `output-workflow-list`, `output-workflow-run`, `output-workflow-start`, `output-workflow-status`, `output-workflow-result`, `output-workflow-trace`, `output-workflow-runs-list`, `output-workflow-stop`

**Skill Structure:**
- YAML frontmatter: `name`, `description`, `allowed-tools` (optional: `model`, `color`)
- Markdown content: Detailed guidance, patterns, examples, symptom documentation

## Output SDK Conventions

When working with Output SDK workflows, follow these rules:

### ES Modules
All imports MUST use `.js` extension:
```typescript
import { stepName } from './steps.js';
```

### HTTP and LLM Clients
- Never use `axios` directly - use `@output.ai/http` wrapper
- Never make direct LLM calls - use `@output.ai/llm` wrapper with `generateText`

### Workflow Structure
```typescript
import { workflow, z } from '@output.ai/core';
import { stepName } from './steps.js';

const inputSchema = z.object({ /* ... */ });
const outputSchema = z.object({ /* ... */ });

export default workflow({
  name: 'workflow-name',
  description: 'Description',
  inputSchema,
  outputSchema,
  fn: async input => {
    const result = await stepName(input);
    return { result };
  }
});
```

### Prompt Files
LLM prompts use the `.prompt` extension with Liquid.js templating:
```
---
provider: anthropic
model: claude-sonnet
temperature: 0.7
---

<assistant>
You are a helpful assistant.
</assistant>

<user>
{{ variable }}
</user>
```

## Workflow CLI Commands

```bash
# List available workflows
npx output workflow list

# Run workflow synchronously (waits for result)
npx output workflow run <workflowName> --input '<json>'
npx output workflow run <workflowName> --input path/to/scenario.json

# Start workflow asynchronously
npx output workflow start <workflowName> --input '<json>'

# Check workflow status
npx output workflow status <workflowId>

# Get workflow result
npx output workflow result <workflowId>

# Debug failed workflow
npx output workflow debug <workflowId> --format json

# List workflow runs
npx output workflow runs list [workflowName] --limit 10

# Generate workflow skeleton
npx output workflow generate --skeleton
```

## Plan and Task Management

Plans are stored in `.outputai/plans/` with naming convention:
```
.outputai/plans/YYYY_MM_DD_<workflow_name>_<task_name>/
  PLAN.md    # Implementation blueprint
  TASK.md    # Progress tracking
```

## Working With This Repository

### Plugin Development Workflow

**Creating a New Command:**
1. Create markdown file in `plugins/{plugin-name}/commands/{command-name}.md`
2. Add YAML frontmatter with `argument-hint`, `description`, `version`, `model`
3. Define XML process flow with numbered `<step>` elements
4. Specify subagent delegation using `subagent=""` attributes where needed
5. Include pre-flight and post-flight validation steps

**Creating a New Agent:**
1. Create markdown file in `plugins/{plugin-name}/agents/{agent-name}.md`
2. Add YAML frontmatter with `name`, `description`, `tools`, `model`, `color`
3. Define identity, core mission, and expertise domains
4. Document when and how the agent should be invoked

**Creating a New Skill:**
1. Create directory `plugins/{plugin-name}/skills/{skill-name}/`
2. Create `SKILL.md` with YAML frontmatter: `name`, `description`, `allowed-tools`
3. Document patterns, examples, and symptom triggers
4. Categorize skill (dev, error, meta, operational)

### Key Architectural Patterns

**Process Flow Pattern:**
Commands follow a structured XML-based process with numbered steps:
1. Arguments analysis and validation
2. Pre-flight validation (`output-meta-pre-flight` skill) - **REQUIRED**
3. Context gathering (with `workflow-context-fetcher` subagent)
4. Main operations (with appropriate subagents)
5. Post-flight verification (`output-meta-post-flight` skill)

**Subagent Delegation:**
Steps can specify subagents via `subagent=""` XML attribute - these MUST be used when specified.
```xml
<step number="1" name="context_gathering" subagent="workflow-context-fetcher">
  ### Step 1: Context Gathering
  [task definition]
</step>
```

**Skill Invocation:**
Agents invoke skills using:
```
EXECUTE: Claude Skill: `skill-name`
```

**Template Variables:**
Commands use template variables that are replaced at runtime:
- `$ARGUMENTS` - All user-provided arguments
- `$1`, `$2`, `$3` - Positional arguments
- `{ WORKFLOW_NAME }` - Derived from context

### Plugin Distribution

**Marketplace Model:**
- Central registry at `.claude-plugin/marketplace.json`
- Each plugin specifies its source directory
- Installation: `claude /plugin marketplace add growthxai/output-claude-plugins`
- Then: `claude /plugin install outputai@outputai`

**Versioning:**
- Use semantic versioning in `plugin.json`
- Version fields in command/agent definitions
- Allows independent plugin updates
