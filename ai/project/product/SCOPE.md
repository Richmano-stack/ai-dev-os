> **Type: PROJECT-SPECIFIC** | Customize for your product. Update as requirements and context evolve.

# Scope Definition

---

## Purpose

SCOPE.md defines **boundaries** for the current development phase. It prevents scope creep by making explicit what is in, out, and deferred. AI agents and developers must consult this before starting work.

**Update this file when:**

- A new development phase begins (MVP → v1, etc.)
- Scope is formally expanded or reduced by stakeholders
- A feature is explicitly deferred or descoped
- Phase gate criteria are met and the next phase is unlocked

**Do not update for:** ticket-level adjustments that stay within already-approved scope.

---

## Instructions

1. **Define the current phase** with a name, target date, and gate criteria.
2. **List in-scope capabilities** as concrete, verifiable items — not vague themes.
3. **List out-of-scope items** explicitly. "Not now" is not the same as "never."
4. **Separate deferred items** with a target phase so they are not forgotten.
5. **Reference the PRD and user stories** — scope should trace back to approved product docs.
6. **Get stakeholder sign-off** before agents or developers begin implementation.
7. **When in doubt, mark out-of-scope.** Scope can be expanded deliberately; creep cannot be undone easily.

---

## Template

```markdown
# Scope: <!-- FILL: Phase name -->

**Phase:** <!-- FILL: e.g., MVP -->
**Target date:** <!-- FILL: YYYY-MM-DD -->
**Last updated:** <!-- FILL: YYYY-MM-DD -->
**Approved by:** <!-- FILL: name or role -->

---

## Phase Gate Criteria

<!-- FILL: Conditions that must be met before this phase is considered complete. -->

- [ ] <!-- FILL -->

---

## In Scope

<!-- FILL: Capabilities included in this phase. Be specific. -->

| ID | Capability | User Story | Notes |
|----|------------|------------|-------|
| S-001 | <!-- FILL --> | <!-- FILL: US-xxx --> | <!-- FILL --> |

---

## Out of Scope

<!-- FILL: Explicitly excluded. Will not be built in this phase or possibly ever. -->

| ID | Capability | Reason |
|----|------------|--------|
| O-001 | <!-- FILL --> | <!-- FILL --> |

---

## Deferred

<!-- FILL: Planned for a future phase. Include target phase. -->

| ID | Capability | Target Phase | User Story |
|----|------------|--------------|------------|
| D-001 | <!-- FILL --> | <!-- FILL: v1 --> | <!-- FILL: US-xxx --> |

---

## Technical Boundaries

<!-- FILL: Stack, integration, or infrastructure limits for this phase. -->

- <!-- FILL -->

---

## Change Log

| Date | Change | Approved by |
|------|--------|-------------|
| <!-- FILL --> | <!-- FILL --> | <!-- FILL --> |
```

---

## Examples

> **Note:** Fictional example for format demonstration only.

```markdown
# Scope: MVP

**Phase:** MVP
**Target date:** 2026-03-01
**Last updated:** 2026-01-20
**Approved by:** Product Lead

---

## Phase Gate Criteria

- [ ] All P0 user stories marked done
- [ ] Core auth and dashboard flows pass E2E tests
- [ ] Deployed to staging with no P0/P1 known issues

---

## In Scope

| ID | Capability | User Story | Notes |
|----|------------|------------|-------|
| S-001 | GitHub OAuth sign-in | US-001 | Org members only |
| S-002 | Task dashboard | US-002 | Read-only for MVP |
| S-003 | Task status update | US-003 | Drag-and-drop deferred |

---

## Out of Scope

| ID | Capability | Reason |
|----|------------|--------|
| O-001 | Mobile native app | Web-responsive sufficient for MVP |
| O-002 | Billing and subscriptions | No monetization in MVP |
| O-003 | Real-time collaboration | Complexity; polling acceptable |

---

## Deferred

| ID | Capability | Target Phase | User Story |
|----|------------|--------------|------------|
| D-001 | Multi-factor authentication | v1 | US-010 |
| D-001 | Task comments | v1 | US-015 |
| D-003 | Email notifications | v1 | US-020 |

---

## Technical Boundaries

- PostgreSQL via managed provider (no self-hosted DB)
- Deployment on Vercel only
- No third-party analytics beyond basic page views
```
