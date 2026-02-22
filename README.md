# AgentEM - Engineering Management Agents for Claude Code

6 AI agents that handle specs, ticket decomposition, risk detection, review routing, release management, and sprint retrospectives. Encoded with your team's engineering judgment.

## Install

```
/plugin marketplace add anicol/engineering-agents
/plugin install agentem@engineering-agents
```

## What you get

| Agent | What it does |
|-------|-------------|
| **Spec Generator** | Turns product briefs into implementation specs that respect your architecture and team constraints |
| **Ticket Decomposer** | Breaks specs into right-sized tickets with acceptance criteria, estimates, and sprint sequencing |
| **Risk Detector** | Scans for stale PRs, blocked work, scope creep, capacity overload, and dependency risks |
| **Review Orchestrator** | Maps PRs to the right reviewers based on ownership, expertise, and load balance |
| **Release Manager** | Generates release notes, changelog, go/no-go checklist, and stakeholder updates |
| **Retro Analyzer** | Pulls sprint metrics, identifies patterns, generates retro docs, proposes learnings updates |

## Quick start

1. **Install the plugin** and open your project in Claude Code.

2. **Scaffold context files:**
   ```
   /agentem:init
   ```
   This creates 7 context files in `context/` - your product strategy, architecture map, team topology, capacity, review standards, spec conventions, and learnings.

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
| `/agentem:init` | Scaffold `context/` directory with 7 template files |
| `/agentem:doctor` | Check which context files exist and how complete they are |
| `/agentem:sprint-plan` | End-to-end: spec, tickets, risk scan |

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

## What this doesn't include

- **Automation** - No cron scheduling, webhook triggers, or Slack delivery. Agents run when you invoke them.
- **Integrations** - No Linear/Jira/Slack API connections. Agents use `gh` CLI when available, filesystem otherwise.
- **Intelligence layer** - No confidence-based routing or autonomous execution. Agents run on request, with human review.

These are part of the paid consulting tier. The plugin gives you the agents and context structure. The consulting engagement adds extraction interviews, automation, integrations, and tuning.

## Ready to go further?

Most teams get the plugin running in 30 minutes, then stall on context because extracting engineering judgment is a different skill than prompting.

[Book a 30-minute call](https://calendly.com/alistair_nicol/30-min) - we'll map your workflow and show you what the system looks like with your team's actual context.

## Development

```
git clone https://github.com/anicol/engineering-agents.git
claude --plugin-dir ./engineering-agents/agentem
```

---

Built by [AgentEM](https://agentem.io)
