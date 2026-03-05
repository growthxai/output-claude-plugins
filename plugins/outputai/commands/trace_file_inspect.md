---
argument-hint: [workflow-name-or-run-id]
description: Inspect a local trace file — debug view with step inputs/outputs as JSON
version: 0.1.0
---

Debug view of an Output.ai workflow trace — shows workflow input and each step with raw inputs/outputs as JSON code blocks.

$ARGUMENTS - Either a workflow name (e.g. `context_competitors`) or a workflow run ID. If empty, use the most recent trace across all workflows.

## Instructions

1. **Find the trace JSON file:**
   - Trace files live in `logs/runs/<workflow_name>/` as JSON files
   - Filenames follow the pattern: `<timestamp>_<workflow_id>.json`
   - If a workflow name is given, find the latest `.json` file in `logs/runs/<workflow_name>/`
   - If a run ID is given, search across all `logs/runs/*/` folders for a file containing that ID in its filename
   - If no argument, find the most recently modified `.json` file across all `logs/runs/*/` folders

2. **Read the trace JSON file.** It may be large — use `offset` and `limit` params to read in chunks if needed. The structure is:
   ```
   {
     "id": "...",
     "kind": "workflow",
     "name": "workflow_name",
     "startedAt": <epoch_ms>,
     "endedAt": <epoch_ms>,
     "input": { ... },
     "output": { "output": { ... }, "trace": { ... } },
     "children": [
       {
         "id": "...",
         "kind": "step",
         "name": "workflow_name#stepName",
         "startedAt": <epoch_ms>,
         "endedAt": <epoch_ms>,
         "input": { ... },
         "output": { ... },
         "children": [
           { "kind": "http", "input": { ... }, "output": { ... } },
           { "kind": "llm", "input": { ... }, "output": { "usage": { ... } } }
         ]
       }
     ]
   }
   ```

3. **Save a markdown file to `tmp/trace_inspect_<workflow_name>_<id>.md`** with these sections:

   ### Header
   - Workflow name, ID, timestamps (ISO format), total duration (human-readable like "4.2s" or "1m 23s")

   ### Workflow Input
   - Render as a JSON code block:
   ```json
   { ... }
   ```

   ### Steps
   For each child in `children` array, in order:

   **`stepName`** (extract part after `#`) — duration

   Input:
   ```json
   { ... }
   ```

   Output:
   ```json
   { ... }
   ```

   - If a step has sub-children of kind `llm`, add a note with prompt name and token usage
   - If a step has sub-children of kind `http`, add a note with the URL/method

   ### Workflow Output
   - Render `output.output` as a JSON code block (skip the `output.trace` field)

4. **Tell the user** the saved file path and a one-line summary.

## Important
- This is a DEBUG view — keep data raw as JSON code blocks. Don't convert to markdown tables or prose.
- Truncate string values longer than 1000 chars inline with `"...[truncated, 5432 chars]"` so the JSON stays readable
- Format durations as human-readable (e.g. "4.2s", "1m 23s")
