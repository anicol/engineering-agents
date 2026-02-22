# Spec Standards

## Spec Structure
<!-- Agents follow this structure when generating specs. Customize to match your team's conventions. -->

Every spec should include:

1. **Problem Statement** — What problem are we solving and for whom? Grounded in evidence, not assumptions.
2. **Proposed Solution** — Describe at the user experience level, not implementation detail.
3. **Success Metrics** — 2-4 measurable outcomes. At least 1 leading indicator.
4. **Scope** — Explicit in-scope AND out-of-scope items with rationale for each exclusion.
5. **Technical Approach** — Architecture decisions, dependencies, data model changes, API changes.
6. **Risks & Mitigations** — Table format: risk, likelihood, impact, mitigation.
7. **Rollout Plan** — Feature flags, percentage rollout, rollback criteria.
8. **Open Questions** — With owners and target resolution dates.

## Conventions
- [e.g., "All specs live in specs/{feature-slug}/spec.md"]
- [e.g., "Specs must be reviewed by at least one engineer and one PM before ticket decomposition"]
- [e.g., "XL initiatives require phased specs — no single spec should span more than 1 sprint of work"]

## Quality Bar
- References specific architecture components (not hand-waving)
- Includes measurable success metrics (not "improve performance")
- Has explicit scope boundaries (prevents scope creep during implementation)
- Identifies risks with concrete mitigations (not "we'll figure it out")
