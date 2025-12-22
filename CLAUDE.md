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

### Commands
Commands are slash commands that orchestrate complex workflows. They are defined as markdown files with YAML frontmatter specifying:
- `argument-hint`: Expected arguments
- `description`: Command purpose
- `model`: Preferred model (typically `opus` for complex tasks)

### Agents (Subagents)
Agents are specialized assistants invoked by commands or other agents. They have focused expertise:
- `workflow-planner`: Designs workflow architecture and creates implementation blueprints
- `workflow-debugger`: Analyzes workflow execution traces and identifies issues
- `workflow-quality`: Reviews code quality and validates implementations
- `workflow-prompt-writer`: Creates and optimizes LLM prompt templates
- `workflow-context-fetcher`: Gathers documentation and existing patterns

### Skills
Skills are reusable knowledge modules that provide specific guidance. Categories include:
- **Workflow operations**: `output-workflow-run`, `output-workflow-start`, `output-workflow-list`, etc.
- **Error patterns**: `output-error-zod-import`, `output-error-try-catch`, `output-error-nondeterminism`, etc.
- **Meta operations**: `output-meta-pre-flight`, `output-meta-post-flight`, `output-meta-project-context`

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

## Process Flow Pattern

Commands follow a structured XML-based process with numbered steps:
1. Pre-flight validation (`output-meta-pre-flight` skill)
2. Context gathering (with `workflow-context-fetcher` subagent)
3. Main operations (with appropriate subagents)
4. Post-flight verification (`output-meta-post-flight` skill)

Steps can specify subagents via `subagent=""` XML attribute - these MUST be used when specified.
