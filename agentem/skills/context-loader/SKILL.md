---
name: context-loader
description: Reads and validates context files before agent execution
user-invocable: false
---

# Context Loader

Before performing your primary task, load and validate the project's context files.

## Steps

1. **Find context directory.** Look for a `context/` directory in the project root.

2. **If no context directory exists:**
   - Note that context files have not been set up yet.
   - Recommend running `/agentem:init` to scaffold the context directory.
   - Proceed with the agent's primary task using best-effort reasoning, but note that output quality will be limited without context.

3. **If context directory exists, read all `.md` files found in `context/`:**
   - `context/product/strategy.md`
   - `context/architecture/system-map.md`
   - `context/team/topology.md`
   - `context/team/capacity.md`
   - `context/standards/review-playbook.md`
   - `context/standards/spec-standards.md`
   - `context/learnings/what-doesnt.md`
   - Any additional `.md` files present

4. **Check for placeholder content.** Scan each file for `[placeholder]` patterns (text in square brackets with uppercase first letter, e.g. `[Your Product Name]`, `[Team Name]`). If a file has many unfilled placeholders, note which files need filling.

5. **Load agent state** from `context/agent-state.json` if it exists:
   - Read the file and parse the JSON
   - For the current agent, extract: `last_run`, `run_count`, `last_summary`
   - Identify `active` signals — do not re-alert on signals already reported
   - Identify `dismissed` signals — skip these entirely
   - Review `feedback` history — note any patterns (e.g., signal types consistently rated "No")
   - Review `actions_taken` — know what actions were already executed
   - If the file does not exist, note this is a first run (no prior state)

6. **Load autonomy config** from `context/autonomy.yaml` if it exists:
   - Read the file and parse the YAML
   - Summarize for the current agent: which action types are `autonomous`, which `requires_approval`, which `disabled`
   - If the file does not exist, default all actions to `requires_approval`

7. **Summarize available context** before proceeding with the agent's primary task:
   - Which context files are present and filled in
   - Which files are missing or mostly placeholders
   - Any gaps that are relevant to the current task
   - Agent state: last run time, run count, active/dismissed signals (if state exists)
   - Autonomy config: action permissions summary (if config exists)
