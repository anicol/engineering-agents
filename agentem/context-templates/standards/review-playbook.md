# Code Review Playbook

## Philosophy
[Your team's review culture in 2-3 sentences. e.g., "Reviews are collaborative, not adversarial. The goal is to ship better code, not prove who's smarter. Bias toward approval with suggestions."]

## Required Before Merge
- [ ] All CI checks pass
- [ ] At least [N] approvals from code owners
- [ ] No unresolved critical comments
- [ ] Test coverage meets threshold ([X]%)
- [ ] No new lint warnings introduced
- [ ] Documentation updated if public API changed

## What Reviewers Should Focus On
1. **Correctness** — Does it do what the ticket says?
2. **Edge cases** — What happens with bad input, empty state, race conditions?
3. **Readability** — Will someone unfamiliar understand this in 6 months?
4. **Performance** — Any N+1 queries, unbounded loops, missing indexes?
5. **Security** — Input validation, auth checks, data exposure?
6. **Testing** — Are the tests testing behavior, not implementation?

## What Reviewers Should NOT Focus On
- Stylistic preferences already handled by linters
- Alternative approaches that aren't clearly better
- Scope expansion ("while you're here, could you also...")

## Review SLAs
<!-- Agents use these to flag aging PRs in risk detection and review orchestration. -->
- **Small PRs (< 200 lines):** Review within [X] hours
- **Medium PRs (200-500 lines):** Review within [X] business day(s)
- **Large PRs (> 500 lines):** Should have been broken up. If unavoidable, review within [X] business days. Author should provide a walkthrough.

## Patterns We Prefer
- [e.g., "Prefer composition over inheritance"]
- [e.g., "Use early returns to reduce nesting"]
- [e.g., "Database queries go in repository classes, not controllers"]

## Anti-Patterns We Reject
- [e.g., "No raw SQL in route handlers"]
- [e.g., "No console.log in production code"]
- [e.g., "No catch-all error handlers that swallow context"]
