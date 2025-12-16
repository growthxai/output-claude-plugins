---
description: Pre-Flight Checks for Output SDK Workflow Operations
version: 1.0
encoding: UTF-8
---

# Pre-Flight Rules for Output SDK Workflows

## Execution Requirements

- **CRITICAL**: For any step that specifies a subagent in the `subagent=""` XML attribute, you MUST use the specified subagent to perform the instructions for that step
- Process all XML blocks sequentially and completely
- Execute every numbered step in the process_flow EXACTLY as specified

## Output SDK Conventions Check

Before proceeding with any workflow operation, verify:

- **ES Modules**: All imports MUST use `.js` extension for ESM modules
- **HTTP Client**: NEVER use axios directly - always use @output.ai/http wrapper
- **LLM Client**: NEVER use a direct llm call - always use @output.ai/llm wrapper
- **Worker Restarts**: Remember to restart worker after creating workflows: `overmind restart worker`
- **Documentation**: Run `yarn g:workflow-doc` after modifications

## Requirements Gathering Strategy

### Smart Defaults Application
When information is not explicitly provided, apply these defaults:
- **Retry Policies**: 3 attempts with exponential backoff (1s initial, 10s max)
- **OpenAI Models**: Use `gpt-4o` unless specified otherwise
- **Error Handling**: ApplicationFailure patterns with appropriate error types
- **Performance**: Optimize for clarity and maintainability over raw speed
- **Timeouts**: 30 seconds for activities, 5 minutes for workflows

### Critical Information Requirements
Only stop to ask for clarification on:
- Ambiguous input/output structures that cannot be inferred from context
- Specific API keys or services not commonly used in the project
- Non-standard error handling or recovery requirements
- Complex orchestration patterns requiring specific sequencing
- External dependencies not already in the project

## Template Processing Rules

- Use exact templates as provided in each step
- Replace all template variables with actual values:
  - `` - The workflow being planned
  - `` - Root project directory path
  - `` - User-provided requirements
  - `` - Current date in YYYY-MM-DD format
  - `` - Current Output SDK version

## Quality Gates

Before proceeding past pre-flight:
1. Confirm all required context is available
2. Verify understanding of the workflow's purpose
3. Check for existing similar workflows to use as patterns
4. Ensure Output SDK conventions are understood
5. Validate that necessary subagents are available
