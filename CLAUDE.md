# Engineering Agents — Claude Code Plugin

This is the Claude Code plugin distribution of AgentEM. It packages 6 engineering management agents, 3 slash commands, and 8 context file templates into a Claude Code plugin that users install from the marketplace.

## Directory Layout

- `agentem/` — Plugin root (referenced as `$CLAUDE_PLUGIN_ROOT` at runtime)
  - `agents/` — Agent definitions (one `.md` per agent, source of truth)
  - `commands/` — Slash command definitions; includes utility commands (`init.md`, `doctor.md`, etc.) and symlinks to each agent so they're also invocable as `/agentem:<agent-name>`
  - `context-templates/` — Template files copied into user projects by `/agentem:init`
  - `skills/` — Internal skills (e.g., `context-loader`) invoked by agents, not by users
- `.claude-plugin/marketplace.json` — Plugin manifest for marketplace distribution
- `README.md` — User-facing documentation and install instructions

## The 6 Agents

| Agent | Input | Output | Actions | Chains To |
|-------|-------|--------|---------|-----------|
| `spec-generator` | Product brief | Feature spec | save-spec, create-issue | ticket-decomposer, risk-detector |
| `ticket-decomposer` | Feature spec | Tickets + sprint plan | create-issues | risk-detector |
| `risk-detector` | Current repo/project state | Risk digest | create-issue, comment-on-pr | review-orchestrator, ticket-decomposer, retro-analyzer |
| `review-orchestrator` | PR reference | Reviewer assignment + summary | assign-reviewers, nudge-stale-prs | risk-detector |
| `release-manager` | Merged work | Release notes + go/no-go | create-release, save-artifacts | retro-analyzer |
| `retro-analyzer` | Sprint metrics | Retro doc + learnings proposals | update-learnings, save-retro, create-issues | /agentem:doctor, risk-detector |

## Slash Commands

All 6 agents are symlinked into `commands/`, so they're invocable as `/agentem:<agent-name>` in addition to being available as Task subagent types.

| Command | What It Does |
|---------|-------------|
| `/agentem:spec-generator` | Generate a feature spec from a product brief |
| `/agentem:ticket-decomposer` | Break a spec into implementable tickets |
| `/agentem:risk-detector` | Scan for delivery risks |
| `/agentem:review-orchestrator` | Route reviewers to a PR |
| `/agentem:release-manager` | Generate release notes and go/no-go checklist |
| `/agentem:retro-analyzer` | Generate a sprint retro and propose learnings updates |
| `/agentem:sprint-plan` | Chains spec generation → ticket decomposition → risk detection end-to-end |
| `/agentem:init` | Scaffolds `context/` directory with 8 template files + autonomy config |
| `/agentem:doctor` | Checks context files, autonomy config, agent state, environment, and per-agent readiness |
| `/agentem:status` | Dashboard — agent activity, current risks, PR status, sprint health, effectiveness scores |
| `/agentem:watch` | Poll GitHub for events and trigger agents on new/stale/merged PRs |

## How Agents Use Context

Every agent invokes the `context-loader` skill before its primary task. The context-loader:
1. Finds `context/` in the project root
2. Reads all `.md` files within it
3. Checks for unfilled `[Placeholder]` patterns
4. Summarizes what context is available vs missing

If no context directory exists, agents proceed with best-effort reasoning and recommend `/agentem:init`.

## Context Templates (8 files + config)

Templates live in `agentem/context-templates/` and are copied to the user's `context/` directory:

| Template | Purpose | Primary Consumer |
|----------|---------|-----------------|
| `product/strategy.md` | Mission, OKRs, priorities, what you're NOT doing | Spec Generator, Risk Detector |
| `architecture/system-map.md` | Services, ownership, data flows, constraints | Spec Generator, Risk Detector, Review Orchestrator |
| `team/topology.md` | People, roles, ownership map, skill matrix | Ticket Decomposer, Review Orchestrator, Risk Detector |
| `team/capacity.md` | Sprint capacity, estimation approach, planning rules | Ticket Decomposer |
| `standards/review-playbook.md` | Review philosophy, SLAs, focus areas | Review Orchestrator |
| `standards/spec-standards.md` | Spec structure, conventions, quality bar | Spec Generator |
| `learnings/what-doesnt.md` | Anti-patterns to avoid (updated after retros) | All agents |
| `learnings/what-works.md` | Proven patterns to reinforce (updated after retros) | All agents |
| `autonomy.yaml` | Action permissions (autonomous/requires_approval/disabled) | All agents |

## State & Config Files

| File | Purpose | Created By | Git |
|------|---------|-----------|-----|
| `context/autonomy.yaml` | Declares what actions agents can take without asking | `/agentem:init` (template) | Committed |
| `context/agent-state.json` | Persistent agent memory — run history, signals, feedback | First agent run (auto-created) | Gitignored |

## Critical Rules

1. Agent definitions in `agents/` are the source of truth for agent behavior. Do not modify agent behavior without updating the corresponding `.md` file.
2. Context templates in `context-templates/` must stay generic — no real team data. They contain `[Placeholder]` patterns that users fill in.
3. The `context-loader` skill is internal (`user-invocable: false`). Users never call it directly.
4. Slash commands in `commands/` define the user-facing entry points. Each command `.md` is the complete specification.
5. All output paths in agents (e.g., `specs/{feature-slug}/spec.md`) refer to the user's project directory, not the plugin directory.
6. Actions default to `requires_approval` when no `autonomy.yaml` exists. Never execute `gh` commands or write files without checking autonomy config first.
7. `context/agent-state.json` is auto-created on first agent run. Never commit it to git — it is machine-specific.
8. Agent workflow order: Context Load → Analysis → Output → Actions → State Update → Chain Offers → Feedback. All new steps are additive — original steps remain intact.

## Plugin Distribution

- **Manifest:** `.claude-plugin/marketplace.json` defines the plugin for marketplace listing
- **Install:** `claude --plugin-dir ./engineering-agents/agentem` (local dev)
- **Marketplace:** Users install via the Claude Code marketplace
- **Versioning:** Bump `version` in `marketplace.json` for releases

## What This Plugin Does NOT Include

- No automation (cron, webhooks, Slack) — watch mode polls in-session, not as a background daemon
- No API integrations (Linear, Jira, Slack) — agents use `gh` CLI when available, filesystem otherwise
- No external database — state is a local JSON file, not a hosted service

These are part of the full framework (`engineering-agent-framework`) or the consulting tier.
