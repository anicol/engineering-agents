---
description: Generate a spec, decompose into tickets, and scan for risks
---

# /agentem:sprint-plan

Sequential multi-agent orchestration. Takes a feature brief and runs it through spec generation, ticket decomposition, and risk detection.

## Usage
```
/agentem:sprint-plan [feature brief]
```

The feature brief is provided via `$ARGUMENTS`. If no arguments provided, ask the user to describe the feature they want to plan.

## Steps

1. **Spec Generation** — Invoke the spec-generator agent with the user's feature brief from `$ARGUMENTS`. Wait for the spec to be generated and reviewed by the user.

2. **Ticket Decomposition** — Pass the generated spec to the ticket-decomposer agent. Decompose into implementable tickets with acceptance criteria, estimates, and sprint sequencing.

3. **Risk Detection** — Run the risk-detector agent against the plan. Scan for capacity risks, dependency risks, and any signals from the current codebase or project state.

4. **Output Summary:**

```
Sprint Plan Complete
====================

Spec: [feature name]
Tickets: [count] ([total estimate])
Sprint fit: [fits in 1 sprint / needs N sprints]
Risks: [count by severity]

Files created:
  specs/[feature-slug]/spec.md
  [ticket summaries]
  [risk digest]
```

## Notes
- Each step runs sequentially — the output of one feeds the next.
- The user can review and adjust after each step before proceeding.
- If context files are missing, the agents will note gaps but proceed with best effort.
