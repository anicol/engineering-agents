---
description: Check the health of your context files, environment, and agent readiness
---

# /agentem:doctor

Diagnostic command that checks context file completeness, environment variables, and per-agent readiness.

## Steps

1. **Check which context files exist** under `context/` in the project root. Look for:
   - `context/product/strategy.md`
   - `context/architecture/system-map.md`
   - `context/team/topology.md`
   - `context/team/capacity.md`
   - `context/standards/review-playbook.md`
   - `context/standards/spec-standards.md`
   - `context/learnings/what-doesnt.md`
   - `context/learnings/what-works.md`

2. **For each file that exists**, assess completeness:
   - Count total words
   - Count placeholder patterns matching `\[[A-Z][^\]]*\]` (e.g., `[Your Product Name]`, `[Team Name]`)
   - Classify as:
     - **Ready** — > 50 words and < 3 placeholders remaining
     - **Needs work** — file exists but mostly placeholders or very short
     - **Missing** — file doesn't exist

3. **Check autonomy config.** Look for `context/autonomy.yaml`:
   - If it exists, parse it and report:
     - Number of actions configured as `autonomous`
     - Number of actions configured as `requires_approval`
     - Number of actions configured as `disabled`
     - Escalation policy
   - If it doesn't exist, note that all actions default to `requires_approval`
   - If it exists but has parse errors, flag the error

4. **Check agent state.** Look for `context/agent-state.json`:
   - If it exists, report per-agent: last run time, run count, active signal count
   - If it doesn't exist, note "No agent runs recorded yet"

5. **Check environment variables.** Read the YAML frontmatter from each agent file in `${CLAUDE_PLUGIN_ROOT}/agents/*.md`. Extract the `requires.env` list from each agent. Collect all unique env var names across agents. For each env var:
   - Run `echo $VAR_NAME` to check if it is set (non-empty)
   - Also check: `which gh` (for GITHUB_TOKEN — the `gh` CLI is the primary GitHub interface)
   - Check optional integration vars: `LINEAR_API_KEY`, `SLACK_BOT_TOKEN`
   - Classify as **OK** (set) or **Not set**

6. **Assess per-agent readiness.** For each agent, cross-reference its `requires.env` and `requires.context` against the results from steps 1-5. Classify each agent as:
   - **Ready** — all required env vars set AND all required context files are Ready
   - **Partial** — all required env vars set BUT some required context files are Missing or Needs work
   - **Blocked** — one or more required env vars are not set

7. **Output a diagnostic report:**

```
AgentEM Context Health Check
============================

Context Files:
  context/product/strategy.md ............. Ready (245 words, 0 placeholders)
  context/architecture/system-map.md ...... Needs work (32 words, 8 placeholders)
  context/team/topology.md ................ Missing
  context/team/capacity.md ................ Missing
  context/standards/review-playbook.md .... Ready (180 words, 1 placeholder)
  context/standards/spec-standards.md ..... Missing
  context/learnings/what-doesnt.md ........ Needs work (15 words, 4 placeholders)
  context/learnings/what-works.md ......... Needs work (12 words, 4 placeholders)

Status: 2 of 8 context files ready

Environment:
  GITHUB_TOKEN ............................ OK
  Optional integrations:
  LINEAR_API_KEY .......................... Not set
  SLACK_BOT_TOKEN ......................... Not set

Agent Readiness:
  spec-generator .......................... Partial (context/architecture/system-map.md needs work)
  ticket-decomposer ....................... Blocked (context/team/topology.md missing)
  risk-detector ........................... Partial (context/team/topology.md missing)
  review-orchestrator ..................... Partial (context/team/topology.md missing)
  release-manager ......................... Ready
  retro-analyzer .......................... Partial (context/learnings/what-doesnt.md needs work)

Status: 1 of 6 agents ready

Autonomy Config:
  context/autonomy.yaml ..................... Present
  Autonomous actions: 0
  Requires approval: 9
  Disabled actions: 0
  Escalation policy: log-only

Agent State:
  risk-detector ............................. Last run: 2h ago (5 runs, 3 active signals)
  spec-generator ............................ Last run: 1d ago (2 runs)
  ticket-decomposer ......................... No runs recorded
  review-orchestrator ....................... No runs recorded
  release-manager ........................... No runs recorded
  retro-analyzer ............................ No runs recorded
```

8. **Suggest the next action**, computed dynamically based on what unblocks the most agents:
   - Count how many agents each missing/needs-work item affects
   - Suggest the single item that unblocks or improves the most agents
   - If an env var is missing and blocks agents, prioritize that over context files
   - Fall back to the static priority list only if all else is equal:
     1. `product/strategy.md` — grounds all agent output
     2. `architecture/system-map.md` — needed for specs and risk detection
     3. `team/topology.md` — needed for ticket assignment and review routing
     4. `team/capacity.md` — needed for sprint planning
     5. `standards/review-playbook.md` — needed for review orchestration
     6. `standards/spec-standards.md` — needed for spec generation
     7. `learnings/what-doesnt.md` — improves quality over time
     8. `learnings/what-works.md` — reinforces proven patterns

9. **Next step prompt** — depends on status:

   - **If any agents are Blocked**: suggest the fix that unblocks the most agents:
     ```
     Fix first: set GITHUB_TOKEN (unblocks 6 agents)
     ```

   - **If any agents are Partial but none Blocked**: suggest the context file that moves the most agents to Ready:
     ```
     Next: Fill in context/team/topology.md — moves 3 agents from Partial to Ready
     Need help extracting your engineering context? https://agentem.io/guide.html
     ```

   - **If all 6 agents are Ready**: prompt the user to try an agent:
     ```
     All agents ready. Try one of these:

       /agentem:sprint-plan <feature brief>    End-to-end: spec → tickets → risk scan
       "Generate a spec for <feature>"         Spec only
       "Scan for delivery risks"               Risk detection on current work
       "Who should review PR #123?"            Review routing
     ```
