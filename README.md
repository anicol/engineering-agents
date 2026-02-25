# AgentEM - Engineering Management Agents for Claude Code

6 AI agents that handle specs, ticket decomposition, risk detection, review routing, release management, and sprint retrospectives. They remember what they've done, take real actions, chain intelligently, and learn from your feedback. Encoded with your team's engineering judgment.

## Install

```
/plugin marketplace add anicol/engineering-agents
/plugin install agentem@engineering-agents
```

## What you get

| Agent | What it does |
|-------|-------------|
| **Spec Generator** | Turns product briefs into specs, saves them, creates tracking issues |
| **Ticket Decomposer** | Breaks specs into tickets, creates GitHub issues in batch or individually |
| **Risk Detector** | Scans for risks, creates issues for critical risks, comments on stale PRs |
| **Review Orchestrator** | Assigns reviewers to PRs, nudges stale reviews, balances review load |
| **Release Manager** | Generates release artifacts, creates GitHub releases, saves changelogs |
| **Retro Analyzer** | Generates retro docs, updates learnings, creates action item issues |

## Quick start

1. **Install the plugin** and open your project in Claude Code.

2. **Scaffold context files:**
   ```
   /agentem:init
   ```
   This creates 8 context files in `context/` - your product strategy, architecture map, team topology, capacity, review standards, spec conventions, and learnings (what works + what doesn't).

3. **Fill in context files.** Start with `context/product/strategy.md`. The more specific you are, the better the agents perform. "We use microservices" is useless. "We have 4 services: auth (Python/FastAPI), api (TypeScript/Express), worker (Go), dashboard (Next.js)" is useful.

4. **Check your progress:**
   ```
   /agentem:doctor
   ```

5. **Run an agent.** Ask Claude to generate a spec, decompose tickets, scan for risks, or any of the 6 agent tasks.

6. **Run the full flow:**
   ```
   /agentem:sprint-plan Build user notification preferences
   ```
   This chains spec generation, ticket decomposition, and risk detection.

## Commands

| Command | What it does |
|---------|-------------|
| `/agentem:init` | Scaffold `context/` directory with 8 template files + autonomy config |
| `/agentem:doctor` | Check context files, autonomy config, agent state, and agent readiness |
| `/agentem:sprint-plan` | End-to-end: spec, tickets, risk scan |
| `/agentem:status` | Dashboard: agent activity, risks, PR status, sprint health, effectiveness |
| `/agentem:watch` | Poll GitHub for events and trigger agents (new PRs, stale PRs, merges) |

## Context files

The agents are only as good as the context you give them. Generic prompts produce generic output.

| File | What it does | Who uses it |
|------|-------------|-------------|
| `product/strategy.md` | Mission, OKRs, priorities, what you're NOT doing | Spec Generator, Risk Detector |
| `architecture/system-map.md` | Services, ownership, data flows, constraints | Spec Generator, Risk Detector, Review Orchestrator |
| `team/topology.md` | People, roles, ownership map, skill matrix | Ticket Decomposer, Review Orchestrator, Risk Detector |
| `team/capacity.md` | Sprint capacity, estimation approach, planning rules | Ticket Decomposer |
| `standards/review-playbook.md` | Review philosophy, SLAs, focus areas, patterns | Review Orchestrator |
| `standards/spec-standards.md` | Spec structure, conventions, quality bar | Spec Generator |
| `learnings/what-doesnt.md` | Anti-patterns to avoid (updated after retros) | All agents |
| `autonomy.yaml` | What agents can do without asking | All agents |

## Autonomy config

By default, agents ask before taking any action. Edit `context/autonomy.yaml` to control this:

```yaml
# Actions agents execute without asking
autonomous:
  risk-detector:
    - scan

# Actions that require your approval (default)
requires_approval:
  review-orchestrator:
    - assign-reviewers
  spec-generator:
    - save-spec

# Actions agents will never take
disabled: {}
```

Agents also remember what they've done between runs, learn from your feedback, and offer to chain to relevant follow-up agents when they finish.

## Security & permissions

Agents use the `gh` CLI for all GitHub interactions. Here's what they can do, grouped by risk:

| Risk tier | Actions | Agents | gh command |
|-----------|---------|--------|------------|
| **Read-only** | Scan repos, PRs, issues | Risk Detector | `gh pr list`, `gh issue list` |
| **Write (local)** | Save specs, retros, release notes to your repo | Spec Generator, Release Manager, Retro Analyzer | File writes only |
| **Write (GitHub)** | Create issues, comment on PRs | All 6 agents | `gh issue create`, `gh pr comment` |
| **Write (assignments)** | Assign PR reviewers, create releases | Review Orchestrator, Release Manager | `gh pr edit --add-reviewer`, `gh release create` |

**Agents never perform destructive actions.** They will not close issues, merge PRs, delete branches, force-push, or modify sprint scope. These are outside agent scope by design.

Every action above defaults to `requires_approval` — the agent shows you the exact command and asks before executing. You control this per-action in `context/autonomy.yaml`. The template includes risk tier annotations so you can make informed decisions about what to make autonomous.

## What this doesn't include

- **Background automation** - Watch mode runs in-session. No cron scheduling, webhook triggers, or Slack delivery.
- **Integrations** - No Linear/Jira/Slack API connections. Agents use `gh` CLI when available, filesystem otherwise.

These are part of the paid consulting tier. The plugin gives you the agents, actions, memory, and context structure. The consulting engagement adds extraction interviews, background automation, integrations, and tuning.

## Ready to go further?

Most teams get the plugin running in 30 minutes, then stall on context because extracting engineering judgment is a different skill than prompting.

[Book a 30-minute call](https://calendly.com/alistair_nicol/30-min) - we'll map your workflow and show you what the system looks like with your team's actual context.

## Staying up to date

Third-party plugins don't auto-update by default. To get new versions automatically, run:

```
/plugin marketplace update
```

This takes you to the marketplace manager where you can enable **auto-update** for `engineering-agents`. Once enabled, new versions are pulled automatically at session start.

## Development

```
git clone https://github.com/anicol/engineering-agents.git
claude --plugin-dir ./engineering-agents/agentem
```

---

Built by [AgentEM](https://agentem.io)
