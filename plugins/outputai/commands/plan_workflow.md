---
argument-hint: [workflow-description-and-additional-instructions]
description: Workflow Planning Command for Output SDK
version: 0.0.1
model: opus
---

Your task is to generate a comprehensive Output.ai workflow implementation plan in markdown format.

The plan will be displayed to the user who can then decide what to do with it.

Please respond with only the final version of the plan.

Use the todo tool to track your progress through the plan creation process.

# Plan Creation Rules

## Overview

Generate detailed specifications for implementation of a new workflow.


<process_flow>

<step number="0" name="arguments_analysis">

### Step 0: Arguments Analysis

Analyze the arguments provided to the command:

{ $ARGUMENTS }

<substep number="0" name="arguments_analysis">

Ensure they have provided:
  - workflow_description: The description of the workflow to be created
  - additional_instructions: Additional instructions for the workflow

If not, ask the user for the missing information.
</substep>

<substep number="1" name="pre_flight_check">
  EXECUTE: Claude Skill: `output-meta-pre-flight`
</substep>

<step number="1" name="context_gathering" subagent="workflow-context-fetcher">

### Step 1: Context Gathering

Take the time to gather all the context you need to create a comprehensive plan.
1. Read any given files or links
2. Find any related workflows in the project
3. Read the projects documentation files

</step>

<step number="2" name="requirements_clarification">

### Step 2: Requirements Clarification

Clarify scope boundaries and technical considerations by asking numbered questions as needed to ensure clear requirements before proceeding.

<clarification_areas>
  <scope>
    - in_scope: what is included
    - out_of_scope: what is excluded (optional)
  </scope>
  <technical>
    - functionality specifics
    - UI/UX requirements
    - integration points
  </technical>
</clarification_areas>

<decision_tree>
  IF clarification_needed:
    ASK numbered_questions
    WAIT for_user_response
  ELSE:
    PROCEED schema_definition
</decision_tree>
</step>

<step number="3" name="workflow_design" subagent="workflow-quality">

### Step 3: Workflow Design

Design the workflow with clear single purpose steps and sound orchestration logic.

<thought_process>

  1. Define the workflow name and description
  2. What is a valid output schema for the workflow?
  3. What is a valid input schema for the workflow?
  4. What needs to happen to transform the input into the output?
  5. What are the atomic steps that need to happen to transform the input into the output?
  6. How do these steps relate to each other?
  7. Are any of these steps conditional?
  8. Are any of these steps already defined in the project?
  9. How could these steps fail, and how should we handle them? (retry, backoff, etc.)

</thought_process>

<workflow_template>
```typescript
import { workflow, z } from '@output.ai/core';
import { sumValues } from './steps.js';

const inputSchema = z.object( {
  values: z.array( z.number() )
} );

const outputSchema = z.object( {
  result: z.number()
} );

export default workflow( {
  name: 'simple',
  description: 'A simple workflow',
  inputSchema,
  outputSchema,
  fn: async input => {
    const result = await sumValues( input.values );
    return { result };
  }
} );
```
</workflow_template>

<step number="4" name="step_design" subagent="workflow-quality">
### Step 4: Step Design

Design the steps with clear boundaries.

<step_template>
```typescript
import { step, z } from '@output.ai/core';

export const sumValues = step( {
  name: 'sumValues',
  description: 'Sum all values',
  inputSchema: z.array( z.number() ),
  outputSchema: z.number(),
  fn: async numbers => numbers.reduce( ( v, n ) => v + n, 0 )
} );
```
</step_template>

</step>

<step number="5" name="prompt_engineering" subagent="workflow-prompt-writer">

### Step 5: Prompt Engineering

If any of the steps use an LLM, design the prompts for the steps.

<decision_tree>
  IF step_uses_llm:
    USE prompt_step_template
  ELSE:
    SKIP to step 6
</decision_tree>

<prompt_step_template>
```typescript
import { step, z } from '@output.ai/core';
import { generateText } from '@output.ai/llm';

export const aiSdkPrompt = step( {
  name: 'aiSdkPrompt',
  description: 'Generates a prompt',
  inputSchema: z.object( {
    topic: z.string()
  } ),
  outputSchema: z.string(),
  fn: async ( { topic } ) => {
    const response = await generateText( {
      prompt: 'prompt@v1',
      variables: { topic }
    } );
    return response;
  }
} );

export const finalStep = step( {
  name: 'finalStep',
  outputSchema: z.boolean(),
  fn: async () => true
} );

```
</prompt_step_template>

<prompt_template>
```
---
provider: anthropic
model: claude-sonnet
temperature: 0.7
---

<assistant>
You are a concise assistant.
</assistant>

<user>
Explain about ""
</user>

```

</prompt_template>

</step>

<step number="6" name="testing_strategy">

### Step 6: Testing Strategy

Design the testing strategy for the workflow.
<thought_process>
  1. What unit tests do we need to write?
  2. How can I run the workflow?
  3. What cases do we need to validate?
</thought_process>

</step>

<step number="7" name="generate_plan">
### Step 7: Generate Plan

Generate the complete plan in markdown format.

Note that every implementation should start with running the cli command `npx output workflow generate --skeleton` to create the workflow directory structure.

<file_template>
  <header>
    # Workflow Requirements Document

    > Workflow: [WORKFLOW_NAME]
    > Created: [CURRENT_DATE]
  </header>
  <required_sections>
    - Overview
    - Spec Scope
    - Out of Scope
    - Workflow Design
    - Step Design
    - Testing Strategy
    - Implementation Phases
  </required_sections>
</file_template>

</step>

<step_number="8" name="post_flight_check">


### Step 8: Post-Flight Check

Verify the plan is complete and ready for implementation.

<post_flight_check>
  EXECUTE: Claude Skill: `output-meta-post-flight`
</post_flight_check>

</step>

</process_flow>

<post_flight_check>
  EXECUTE: Claude Skill: `output-meta-post-flight`
</post_flight_check>

---- START ----

Workflow Description and Additional Instructions:

$ARGUMENTS
