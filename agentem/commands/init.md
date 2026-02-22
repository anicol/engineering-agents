---
description: Scaffold context files in your project
---

# /agentem:init

Scaffold the `context/` directory in the user's project root with templates for all 7 context files.

## Steps

1. **Check if `context/` already exists** in the project root.
   - If it exists and contains files, warn the user: "Context directory already exists with files. Proceeding will skip existing files and only create missing ones."
   - If it doesn't exist, create it.

2. **Create the directory structure:**

```
context/
├── product/strategy.md
├── architecture/system-map.md
├── team/topology.md
├── team/capacity.md
├── standards/review-playbook.md
├── standards/spec-standards.md
└── learnings/what-doesnt.md
```

3. **For each file**, read the corresponding template from `${CLAUDE_PLUGIN_ROOT}/context-templates/` and write a copy into the project's `context/` directory. Skip any files that already exist.

4. **Output a summary:**

```
Created context files:
  context/product/strategy.md
  context/architecture/system-map.md
  context/team/topology.md
  context/team/capacity.md
  context/standards/review-playbook.md
  context/standards/spec-standards.md
  context/learnings/what-doesnt.md

Start with context/product/strategy.md — it grounds everything else.
Run /agentem:doctor to check your progress.
```

## Template source mapping

| Target | Template source |
|--------|----------------|
| `context/product/strategy.md` | `${CLAUDE_PLUGIN_ROOT}/context-templates/product/strategy.md` |
| `context/architecture/system-map.md` | `${CLAUDE_PLUGIN_ROOT}/context-templates/architecture/system-map.md` |
| `context/team/topology.md` | `${CLAUDE_PLUGIN_ROOT}/context-templates/team/topology.md` |
| `context/team/capacity.md` | `${CLAUDE_PLUGIN_ROOT}/context-templates/team/capacity.md` |
| `context/standards/review-playbook.md` | `${CLAUDE_PLUGIN_ROOT}/context-templates/standards/review-playbook.md` |
| `context/standards/spec-standards.md` | `${CLAUDE_PLUGIN_ROOT}/context-templates/standards/spec-standards.md` |
| `context/learnings/what-doesnt.md` | `${CLAUDE_PLUGIN_ROOT}/context-templates/learnings/what-doesnt.md` |
