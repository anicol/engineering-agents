---
description: Poll GitHub for events and trigger agents automatically
---

# /agentem:watch

Continuous polling mode that monitors GitHub for events and triggers agents when action is needed. Runs in the active Claude Code session — user must keep the session open.

## Usage
```
/agentem:watch [--interval 5] [--dry-run]
```

Arguments (via `$ARGUMENTS`):
- `--interval N` — poll every N minutes (default: 5, minimum: 1)
- `--dry-run` — show what would trigger but don't run agents

## Steps

1. **Validate prerequisites.**
   - Check `gh` CLI is available. If not, abort: "Watch mode requires the gh CLI. Install it and authenticate with `gh auth login`."
   - Check `context/autonomy.yaml` exists. If not, warn: "No autonomy config found. All triggered actions will require approval. Run /agentem:init to configure."
   - Parse `$ARGUMENTS` for `--interval` and `--dry-run` flags.

2. **Display watch mode banner:**

```
AgentEM Watch Mode
==================
Polling every 5 minutes. Press Ctrl+C to stop.
Autonomy: 0 autonomous, 9 requires approval, 0 disabled

Watching for:
  • New PRs without reviewers → review-orchestrator
  • PRs merged to main → release-manager (offer)
  • Stale PRs (>48h no update) → risk-detector
  • Status update every 3 cycles
```

3. **Start poll loop.** Use Bash `while true` with `sleep`:

Each cycle:

   a. **New PRs without reviewers.** Run `gh pr list --json number,title,createdAt,reviewRequests --jq '.[] | select(.reviewRequests | length == 0)'`.
      - For each unreviewed PR, check state file — skip if review-orchestrator already processed this PR (by number) in the current session.
      - If new unreviewed PR found:
        - Check autonomy config for review-orchestrator actions
        - If `autonomous` or in dry-run: log and trigger/report
        - If `requires_approval`: notify user and ask to trigger review-orchestrator
        - If `disabled`: skip silently

   b. **Merged PRs.** Run `gh pr list --state merged --json number,title,mergedAt --jq '.[] | select(.mergedAt > "{last_check_time}")'`.
      - For each newly merged PR (since last check):
        - Offer: "PR #{number} merged. Run release-manager to update release artifacts?"
        - Respect autonomy config

   c. **Stale PRs.** Run `gh pr list --json number,title,createdAt,updatedAt --jq '.[] | select((.updatedAt | fromdateiso8601) < (now - 172800))'`.
      - For each stale PR (>48h since last update):
        - Check state file — skip if already flagged as stale in current session
        - Trigger risk-detector with focus on stale PR
        - Respect autonomy config

   d. **Status update.** Every 3 cycles, display a brief status:
      ```
      [10:45] Cycle 3 — 0 new events. Next check in 5 minutes.
      ```
      Or if events were found:
      ```
      [10:45] Cycle 3 — 2 events processed (1 reviewer assigned, 1 stale PR flagged). Next check in 5 minutes.
      ```

4. **Handle interruption.** On user Ctrl+C or stop:
   - Display session summary: total cycles, events detected, agents triggered, actions taken
   - Update `context/agent-state.json` with watch session data

```
Watch session ended.
====================
Duration: 45 minutes (9 cycles)
Events detected: 4
Agents triggered: 3 (2 review-orchestrator, 1 risk-detector)
Actions taken: 2 (reviewer assignments)
```

## Dry Run Mode

When `--dry-run` is passed:
- Run all detection logic normally
- Instead of triggering agents, display what WOULD trigger:
  ```
  [DRY RUN] Would trigger review-orchestrator for PR #145 (no reviewers assigned)
  [DRY RUN] Would trigger risk-detector for PR #142 (stale 72h)
  ```
- Do not update state file
- Useful for testing watch configuration before enabling

## Error Handling
- `gh` CLI not available → abort with setup instructions
- GitHub API rate limit → increase interval, warn user
- Agent trigger fails → log error, continue watching (don't crash the loop)
- Network interruption → retry next cycle, warn after 3 consecutive failures
