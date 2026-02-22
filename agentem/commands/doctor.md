---
description: Check the health of your context files
---

# /agentem:doctor

Diagnostic command that checks the completeness of your context files.

## Steps

1. **Check which context files exist** under `context/` in the project root. Look for:
   - `context/product/strategy.md`
   - `context/architecture/system-map.md`
   - `context/team/topology.md`
   - `context/team/capacity.md`
   - `context/standards/review-playbook.md`
   - `context/standards/spec-standards.md`
   - `context/learnings/what-doesnt.md`

2. **For each file that exists**, assess completeness:
   - Count total words
   - Count placeholder patterns matching `\[[A-Z][^\]]*\]` (e.g., `[Your Product Name]`, `[Team Name]`)
   - Classify as:
     - **Ready** — > 50 words and < 3 placeholders remaining
     - **Needs work** — file exists but mostly placeholders or very short
     - **Missing** — file doesn't exist

3. **Output a diagnostic report:**

```
AgentEM Context Health Check
============================

  context/product/strategy.md        Ready (245 words, 0 placeholders)
  context/architecture/system-map.md Needs work (32 words, 8 placeholders)
  context/team/topology.md           Missing
  context/team/capacity.md           Missing
  context/standards/review-playbook.md Ready (180 words, 1 placeholder)
  context/standards/spec-standards.md Missing
  context/learnings/what-doesnt.md   Needs work (15 words, 4 placeholders)

Status: 2 of 7 context files ready

Next: Fill in context/architecture/system-map.md — the agents need your system
topology to generate accurate specs and route reviews correctly.
```

4. **Suggest the next file to work on**, prioritized by impact:
   1. `product/strategy.md` — grounds all agent output
   2. `architecture/system-map.md` — needed for specs and risk detection
   3. `team/topology.md` — needed for ticket assignment and review routing
   4. `team/capacity.md` — needed for sprint planning
   5. `standards/review-playbook.md` — needed for review orchestration
   6. `standards/spec-standards.md` — needed for spec generation
   7. `learnings/what-doesnt.md` — improves quality over time

5. **Conversion surface** — add a single line at the bottom:

```
Need help extracting your engineering context? https://agentem.io/guide.html
```
