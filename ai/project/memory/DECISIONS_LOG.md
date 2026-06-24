> **Type: PROJECT-SPECIFIC** | Customize for your product. Update as requirements and context evolve.

# Decisions Log

---

## Purpose

The decisions log records **significant architectural and technical decisions** with context, rationale, and consequences. It prevents re-litigating settled choices and gives AI agents historical context.

**Update this file when:**

- A non-trivial technical choice is made (framework, pattern, data model approach)
- An existing decision is superseded by a new one
- A deliberate trade-off is accepted with documented reasoning

**Do not log:**

- Routine implementation choices obvious from existing patterns
- Ticket-level bug fix approaches
- Formatting or style preferences (those belong in TECH_RULES.md)

---

## Instructions

1. **Use ADR (Architecture Decision Record) format** for each entry.
2. **Assign a sequential ID:** `DEC-001`, `DEC-002`, etc.
3. **Include status:** `proposed`, `accepted`, `deprecated`, `superseded`.
4. **Write for a future reader** who was not in the discussion.
5. **Document consequences** — both positive and negative.
6. **Link superseded entries** to their replacement.
7. **Keep entries immutable.** Do not edit accepted decisions; add a new entry that supersedes them.

---

## Template

```markdown
# Decisions Log

**Last updated:** <!-- FILL: YYYY-MM-DD -->

---

## DEC-<!-- FILL: 001 -->: <!-- FILL: Short decision title -->

**Date:** <!-- FILL: YYYY-MM-DD -->
**Status:** <!-- FILL: proposed | accepted | deprecated | superseded -->
**Deciders:** <!-- FILL: names or roles -->
**Supersedes:** <!-- FILL: DEC-xxx or N/A -->
**Superseded by:** <!-- FILL: DEC-xxx or N/A -->

### Context

<!-- FILL: What situation or problem prompted this decision? -->

### Decision

<!-- FILL: What was decided? State clearly and specifically. -->

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| <!-- FILL --> | <!-- FILL --> | <!-- FILL --> |

### Consequences

**Positive:**

- <!-- FILL -->

**Negative:**

- <!-- FILL -->

**Neutral:**

- <!-- FILL -->

### References

- <!-- FILL: Links to tickets, PRs, docs -->
```

---

## Examples

> **Note:** Fictional example for format demonstration only.

```markdown
## DEC-001: Use Server Actions for all mutations

**Date:** 2026-01-10
**Status:** accepted
**Deciders:** Tech Lead, Backend Engineer
**Supersedes:** N/A
**Superseded by:** N/A

### Context

The MVP requires create, update, and delete operations for tasks. We need to choose between API Route Handlers and Server Actions for mutations.

### Decision

All data mutations will use Next.js Server Actions with Zod validation. API Route Handlers are reserved for webhooks and third-party callbacks only.

### Alternatives Considered

| Option | Pros | Cons |
|--------|------|------|
| Server Actions | Type-safe, colocated with features, built-in CSRF | Less familiar to some team members |
| API Route Handlers | RESTful, familiar pattern | More boilerplate, separate type contracts |
| tRPC | End-to-end type safety | Additional dependency, overkill for MVP scope |

### Consequences

**Positive:**

- Mutations are colocated in feature modules
- Zod schemas serve as single validation source
- No separate API contract to maintain

**Negative:**

- Team must learn Server Action error handling patterns
- Harder to expose mutations to non-Next.js clients (acceptable for MVP)

### References

- Ticket: TICKET-003
- PR: #12
```
