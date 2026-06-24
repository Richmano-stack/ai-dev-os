> **Type: PROJECT-SPECIFIC** | Customize for your product. Update as requirements and context evolve.

# User Stories

---

## Purpose

User stories translate product goals into **user-facing capabilities** with testable acceptance criteria. They are the bridge between the PRD and implementation tickets.

**Update this file when:**

- New user-facing capabilities are identified
- Acceptance criteria change after stakeholder review
- Stories are completed, deferred, or cancelled
- Priorities shift between epics

**Do not update for:** internal refactors, dependency upgrades, or infrastructure work with no user-visible change (use tickets directly).

---

## Instructions

1. **Group stories by epic** (a cohesive user outcome, not a sprint).
2. **Write stories in user voice:** "As a [persona], I want [capability], so that [benefit]."
3. **Every story needs acceptance criteria** written as checkboxes. Criteria must be testable — no subjective language.
4. **Assign priority:** `P0` (must-have for current phase), `P1` (should-have), `P2` (nice-to-have).
5. **Link to PRD goals** so traceability is clear.
6. **Do not include implementation details** (API endpoints, component names). Those belong in tickets.
7. **One story = one user outcome.** Split large stories before ticketing.
8. **Mark status** on each story: `draft`, `ready`, `in_progress`, `done`, `deferred`.

---

## Template

```markdown
# User Stories

**Last updated:** <!-- FILL: YYYY-MM-DD -->
**Phase:** <!-- FILL: MVP | v1 | v2 -->

---

## Epic: <!-- FILL: Epic name -->

**PRD goal:** <!-- FILL: Which PRD goal this epic supports -->
**Status:** <!-- FILL: draft | active | complete | deferred -->

### US-<!-- FILL: 001 -->: <!-- FILL: Short title -->

**Priority:** <!-- FILL: P0 | P1 | P2 -->
**Status:** <!-- FILL: draft | ready | in_progress | done | deferred -->
**Persona:** <!-- FILL: from PRD personas -->

> As a **<!-- FILL: persona -->**, I want **<!-- FILL: capability -->**, so that **<!-- FILL: benefit -->**.

**Acceptance criteria:**

- [ ] <!-- FILL: Testable criterion -->
- [ ] <!-- FILL: Testable criterion -->

**Out of scope:**

- <!-- FILL: What this story explicitly does not cover -->

**Notes:**

- <!-- FILL: Optional context, links, open questions -->

---

<!-- Repeat story block for each story -->
```

---

## Examples

> **Note:** Fictional example for format demonstration only.

```markdown
## Epic: User Authentication

**PRD goal:** Goal 2 — Give every team member a single view of assigned work
**Status:** active

### US-001: Sign in with organization account

**Priority:** P0
**Status:** ready
**Persona:** Engineer

> As an **engineer**, I want **to sign in with my organization account**, so that **I can access my team's task board without creating a separate password**.

**Acceptance criteria:**

- [ ] User can initiate sign-in from the landing page
- [ ] Successful sign-in redirects to the dashboard
- [ ] Failed sign-in shows a clear error message without exposing system details
- [ ] Session persists across browser restarts for 7 days
- [ ] User can sign out and session is invalidated server-side

**Out of scope:**

- Multi-factor authentication (deferred to US-010)
- Account registration without org invite

---

### US-002: View assigned tasks on dashboard

**Priority:** P0
**Status:** draft
**Persona:** Engineer

> As an **engineer**, I want **to see my assigned tasks on a dashboard**, so that **I know what to work on without asking my team lead**.

**Acceptance criteria:**

- [ ] Dashboard loads within 2 seconds on a standard connection
- [ ] Tasks are grouped by status (To Do, In Progress, Done)
- [ ] Each task shows title, assignee, and due date
- [ ] Empty state displays when no tasks are assigned
```
