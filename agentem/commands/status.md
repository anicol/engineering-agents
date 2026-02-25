---
description: Single-command health snapshot of agents, risks, PRs, and sprint state
---

# /agentem:status

Dashboard command that aggregates agent state, live GitHub data, and feedback scores into a single health snapshot.

## Steps

1. **Load agent state.** Read `context/agent-state.json` if it exists. If it doesn't, note "No agent runs recorded — run an agent first or use /agentem:init to get started."

2. **Agent Activity.** For each agent in the state file, display:
   - Last run time (relative: "2h ago", "1d ago")
   - Total run count
   - Last summary (one-line description from most recent run)
   - If an agent has never run, show "—"

```
Agent Activity:
  risk-detector ........... 2h ago (5 runs) — Found 2 critical, 1 high, 3 medium risks
  spec-generator .......... 1d ago (2 runs) — Generated spec for notification preferences
  review-orchestrator ..... 3d ago (1 run)  — Routed 3 PRs, assigned 5 reviewers
  ticket-decomposer ....... —
  release-manager ......... —
  retro-analyzer .......... —
```

3. **Current Risks.** From the last risk-detector run in the state file:
   - List active signals grouped by severity (Critical/High/Medium/Low)
   - Show count of dismissed signals
   - If risk-detector has never run, show "No risk data — run risk-detector first"

```
Current Risks (from last scan 2h ago):
  Critical: 0
  High: 1 — PR #142 open 72h without review
  Medium: 3
  Low: 2
  Dismissed: 1
```

4. **PR Review Status.** Live query via `gh pr list --json number,title,createdAt,reviewRequests,reviews,state`:
   - Count open PRs
   - Count PRs awaiting review (no approvals)
   - Count PRs exceeding 48h without review
   - If `gh` is not available, note "gh CLI not available — install for live PR data"

```
PR Review Status (live):
  Open PRs: 7
  Awaiting review: 3
  Exceeding 48h SLA: 1 (PR #142)
```

5. **Sprint Health.** Composite assessment based on available data:
   - **GREEN** — No critical/high risks, no PRs exceeding SLA, agents running regularly
   - **YELLOW** — High risks present OR PRs approaching SLA OR agents haven't run in 3+ days
   - **RED** — Critical risks present OR multiple PRs exceeding SLA OR agents haven't run in 7+ days
   - Show the specific factors driving the assessment

```
Sprint Health: YELLOW
  ⚠ 1 PR exceeding 48h review SLA
  ⚠ ticket-decomposer hasn't run this sprint
  ✓ No critical risks
  ✓ risk-detector ran within 24h
```

6. **Agent Effectiveness.** From feedback data in the state file:
   - Show per-agent feedback scores (Yes/Partial/No counts)
   - Only show agents that have feedback data

```
Agent Effectiveness:
  risk-detector ........... 8 useful, 3 partial, 1 not useful (67% useful)
  spec-generator .......... 4 useful, 1 partial (80% useful)
```

7. **Suggested Actions.** Generate 1-3 contextual next steps based on the data above:
   - Prioritize by impact (critical risks > SLA breaches > missing agent runs > feedback improvements)
   - Each suggestion includes the specific command or action to take

```
Suggested Actions:
  1. Run review-orchestrator — PR #142 needs reviewer assignment (72h without review)
  2. Run ticket-decomposer — no tickets decomposed this sprint
  3. Fill in context/team/topology.md — improves review routing accuracy
```

## Error Handling
- No state file → show minimal dashboard with "Run /agentem:init then run an agent to populate this dashboard"
- `gh` CLI not available → skip PR Review Status section, note the gap
- State file corrupted/invalid JSON → warn user, suggest deleting and re-running agents
