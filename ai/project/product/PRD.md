> **Type: PROJECT-SPECIFIC** | Customize for your product. Update as requirements and context evolve.

# Product Requirements Document (PRD)

---

## Purpose

The PRD is the single source of truth for **what** the product is and **why** it exists. It defines the problem space, goals, constraints, and success criteria at a product level — not implementation details.

**Update this file when:**

- Starting a new project or major initiative
- Scope changes at the product level (new goals, retired features)
- Stakeholders agree on revised success metrics or constraints
- A phase gate (MVP → v1 → v2) is reached

**Do not update for:** individual bug fixes, refactors, or ticket-level changes. Those belong in tickets.

---

## Instructions

1. **Replace every `<!-- FILL -->` marker** in the Template section with project-specific content.
2. **Delete sections** that do not apply to your project (note why in a one-line comment if removed).
3. **Keep it concise.** A PRD should be readable in 10–15 minutes. Link to external docs for deep dives.
4. **Separate facts from assumptions.** Mark unvalidated claims explicitly.
5. **Define non-goals.** What you will *not* build is as important as what you will.
6. **Make success metrics measurable.** Avoid vague goals like "improve UX."
7. **Review with stakeholders** before creating tickets from this document.
8. **Version the document.** Increment the version field when making substantive changes.

---

## Template

```markdown
# PRD: <!-- FILL: Product or initiative name -->

**Version:** <!-- FILL: e.g., 1.0 -->
**Last updated:** <!-- FILL: YYYY-MM-DD -->
**Owner:** <!-- FILL: name or role -->
**Status:** <!-- FILL: draft | review | approved | deprecated -->

---

## Problem Statement

<!-- FILL: What problem exists today? Who experiences it? What is the cost of not solving it? -->

## Target Users

<!-- FILL: Primary and secondary user personas. Include role, context, and core need. -->

| Persona | Role | Core Need |
|---------|------|-----------|
| <!-- FILL --> | <!-- FILL --> | <!-- FILL --> |

## Goals

<!-- FILL: What the product must achieve. Use outcome language, not feature lists. -->

1. <!-- FILL -->
2. <!-- FILL -->

## Non-Goals

<!-- FILL: Explicitly out of scope for this initiative. -->

- <!-- FILL -->

## Success Metrics

<!-- FILL: Measurable criteria. Include baseline and target where possible. -->

| Metric | Baseline | Target | Measurement Method |
|--------|----------|--------|--------------------|
| <!-- FILL --> | <!-- FILL --> | <!-- FILL --> | <!-- FILL --> |

## Constraints

<!-- FILL: Technical, legal, timeline, budget, or organizational constraints. -->

- **Timeline:** <!-- FILL -->
- **Budget:** <!-- FILL -->
- **Technical:** <!-- FILL -->
- **Compliance:** <!-- FILL -->

## Assumptions

<!-- FILL: Beliefs that must hold true. Mark as validated or unvalidated. -->

| Assumption | Status |
|------------|--------|
| <!-- FILL --> | unvalidated |

## Dependencies

<!-- FILL: External systems, teams, or approvals required. -->

- <!-- FILL -->

## Risks

<!-- FILL: Product-level risks and mitigations. -->

| Risk | Impact | Likelihood | Mitigation |
|------|--------|------------|------------|
| <!-- FILL --> | <!-- FILL --> | <!-- FILL --> | <!-- FILL --> |

## Open Questions

<!-- FILL: Unresolved decisions. Link to tickets when resolved. -->

- [ ] <!-- FILL -->
```

---

## Examples

> **Note:** The following is a fictional example to demonstrate format only. Do not treat it as real project requirements.

```markdown
# PRD: Team Task Board

**Version:** 1.0
**Last updated:** 2026-01-15
**Owner:** Product Lead
**Status:** approved

---

## Problem Statement

Small engineering teams track work across chat, spreadsheets, and ad-hoc notes.
Context is lost, priorities are unclear, and status updates consume meeting time.

## Target Users

| Persona | Role | Core Need |
|---------|------|-----------|
| Engineer | Individual contributor | See assigned work and update status quickly |
| Team Lead | Engineering manager | Understand team capacity and blockers at a glance |

## Goals

1. Reduce time spent on status meetings by 50%.
2. Give every team member a single view of assigned and available work.

## Non-Goals

- Cross-team portfolio management (deferred to v2)
- Time tracking and billing
- Mobile-native app (responsive web only for MVP)

## Success Metrics

| Metric | Baseline | Target | Measurement Method |
|--------|----------|--------|--------------------|
| Weekly status meeting duration | 60 min | 30 min | Calendar audit |
| Tasks updated within 24h of change | 40% | 85% | Product analytics |

## Constraints

- **Timeline:** MVP in 8 weeks
- **Technical:** Must integrate with existing GitHub org for auth
- **Compliance:** SOC 2 Type II hosting required

## Assumptions

| Assumption | Status |
|------------|--------|
| Teams have fewer than 15 members | validated |
| GitHub is the sole auth provider for MVP | unvalidated |

## Open Questions

- [ ] Should guests (non-org members) have read-only access?
```
