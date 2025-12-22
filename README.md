# Output.ai Claude Plugins

Claude Code plugins for Output.ai workflow development - build, debug, and migrate workflows with AI-powered assistance.

## Overview

This repository provides two specialized Claude Code plugins:

### **outputai** - Workflow Development Toolkit
Complete toolkit for building Output SDK workflows with expert AI guidance.

**Features:**
- **Plan workflows** - Generate detailed implementation blueprints with architecture design
- **Build workflows** - Implement workflows following Output SDK best practices
- **Debug workflows** - Analyze execution traces and diagnose issues
- **5 specialized agents** - Planner, debugger, quality reviewer, prompt writer, context fetcher
- **26 expert skills** - Development patterns, error resolution, workflow operations

### **outputai-flow-migrator** - Legacy Migration Tools
Automated migration from Flow SDK to Output SDK with error prevention.

**Features:**
- **Migration planning** - Analyze and plan Flow SDK to Output SDK conversions
- **Automated conversion** - Transform activities, workflows, prompts, and schemas
- **Error prevention** - Detect and fix common migration issues
- **12 migration skills** - Analysis, conversion, validation, and compliance

## Installation

```bash
claude
> /plugin marketplace add growthxai/output-claude-plugins
> /plugin install outputai@outputai
> /plugin install outputai-flow-migrator@outputai
```

After installation, slash commands will be available in Claude CLI autocomplete.

## Quick Start

### Create Your First Workflow

```bash
claude
> /outputai:plan_workflow Create a workflow that fetches weather data from an API and sends email alerts
```

The planner will:
1. Analyze your requirements
2. Design the workflow architecture
3. Create a detailed implementation plan in `.outputai/plans/YYYY_MM_DD_workflow_name/PLAN.md`

### Build the Workflow

```bash
> /outputai:build_workflow .outputai/plans/YYYY_MM_DD_weather_alerts/PLAN.md
```

The builder will:
1. Implement the workflow following the plan
2. Create steps, schemas, and prompt files
3. Follow Output SDK conventions automatically

### Debug a Failed Workflow

```bash
> /outputai:debug_workflow wf_abc123xyz
```

The debugger will:
1. Fetch execution trace from Temporal
2. Analyze failure points
3. Provide root cause analysis and fixes

## Available Commands

### outputai Plugin

| Command | Description |
|---------|-------------|
| `/outputai:plan_workflow [description]` | Generate comprehensive workflow implementation plan |
| `/outputai:build_workflow [plan-path]` | Implement workflow from plan document |
| `/outputai:debug_workflow [workflow-id]` | Debug workflow execution issues |

### outputai-flow-migrator Plugin

| Command | Description |
|---------|-------------|
| `/outputai-flow-migrator:plan_flow_workflow_migration [workflow-path]` | Create migration plan for Flow SDK to Output SDK conversion |

## How It Works

These plugins use a **3-layer architecture**:

1. **Commands** - User-facing slash commands that orchestrate complex workflows
2. **Agents** - Specialized subagents with focused expertise (planning, debugging, quality review, etc.)
3. **Skills** - Reusable knowledge modules for patterns, error fixes, and operations

Commands automatically delegate to appropriate agents and invoke relevant skills based on context.

**Example flow:**
```
User: /outputai:plan_workflow [description]
  ↓
Command: plan_workflow
  ↓ delegates to
Agent: workflow-planner (Opus)
  ↓ invokes
Skills: output-meta-pre-flight, output-dev-folder-structure, output-dev-workflow-function
  ↓ produces
Output: Detailed implementation plan in .outputai/plans/
```

## Agents

### outputai Agents

- **workflow-planner** (Opus) - Designs workflow architecture and creates implementation blueprints
- **workflow-debugger** (Opus) - Analyzes execution traces and identifies root causes
- **workflow-quality** (Sonnet) - Reviews code quality and validates SDK conventions
- **workflow-prompt-writer** (Sonnet) - Creates and optimizes LLM prompt files
- **workflow-context-fetcher** (Haiku) - Efficiently retrieves documentation and patterns

### flow-migrator Agent

- **flow-workflow-migrator** (Opus) - Converts Flow SDK workflows to Output SDK with error prevention

## Skills Categories

### Development Skills
Guide creation of workflows, steps, schemas, prompts, and project structure.

### Error Resolution Skills
Diagnose and fix common issues: schema imports, determinism violations, HTTP clients, etc.

### Workflow Operation Skills
Execute and manage workflows: run, start, status, result, trace, debug.

### Migration Skills
Analyze, convert, and validate Flow SDK to Output SDK migrations.

## Output SDK Conventions

The plugins enforce Output SDK best practices:

- **ES Modules** - All imports use `.js` extension
- **HTTP Client** - Use `@output.ai/http`, never `axios` directly
- **LLM Client** - Use `@output.ai/llm`, never direct provider calls
- **Zod Schemas** - Import `z` from `@output.ai/core`, not `zod` package
- **Determinism** - No random, Date, or I/O operations in workflow functions
- **Step Boundaries** - All I/O isolated to step functions

## Development

Want to extend or customize these plugins?

See **[CLAUDE.md](./CLAUDE.md)** for comprehensive developer documentation:
- Plugin architecture and patterns
- Creating new commands, agents, and skills
- Process flow patterns and subagent delegation
- Output SDK conventions and best practices

## Documentation

- [Output.ai Documentation](https://docs.output.ai)
- [CLAUDE.md - Developer Guide](./CLAUDE.md)
- [Claude Code Documentation](https://docs.anthropic.com/claude-code)

## Support

For issues or questions:
- Output.ai support: team@output.ai
- Plugin issues: Create an issue in this repository
