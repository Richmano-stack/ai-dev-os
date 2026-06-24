> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Ticket Execution Prompt

Copy-paste template for **starting a single ticket** in Cursor Agent mode. Use this per task — not as a global system prompt.

For standing agent identity, see [SYSTEM_PROMPT.md](./SYSTEM_PROMPT.md). For what to read and when, see [CONTEXT_POLICY.md](./CONTEXT_POLICY.md).

---

## When to Use

- You have a ticket file with `status: ready` or `in_progress`
- You want the agent to implement without scope drift
- You are in **Agent mode** (not Ask mode)

## How to Use in Cursor

1. Open Agent chat.
2. `@`-reference your ticket file (required).
3. Copy the prompt below into chat.
4. Replace `TICKET-NNN-your-slug` with your actual ticket path.
5. Send.

**Tip:** You can also `@`-reference the docs listed in the prompt instead of relying on path strings alone.

---

## Feature Ticket (default)

Use for `type: feature` tickets — new capabilities, UI, Server Actions, schemas.

```
Execute @ai/project/tickets/TICKET-NNN-your-slug.md

You are a ticket executor for a Next.js App Router project — not a brainstorming assistant.

## Before writing any code — read in this order

1. The ticket — verify status is ready or in_progress; all blocked_by tickets are done
2. ai/reusable/runtime/CONTEXT_POLICY.md — follow loading rules for this ticket type
3. ai/reusable/workflow/EXECUTION_RULES.md — pre/during/post execution protocol
4. ai/reusable/runtime/AGENT_RULES.md — do/don't and scope enforcement
5. ai/project/memory/PROJECT_CONTEXT.md
6. ai/project/memory/KNOWN_ISSUES.md
7. ai/project/memory/DECISIONS_LOG.md — if this ticket touches architecture or dependencies
8. ai/reusable/stack/TECH_RULES.md
9. ai/reusable/stack/PATTERNS.md — copy patterns; do not invent alternatives
10. ai/reusable/stack/ARCHITECTURE.md

If other tickets are in_progress, check ai/reusable/workflow/PARALLELISM_RULES.md before starting.

## Hard constraints

- Only create, modify, or delete files listed in the ticket's files section
- Only implement scope.in_scope — never scope.out_of_scope
- If a file outside scope must change: STOP and request a ticket scope update
- Production-ready code only — no mocks, stubs, or TODO placeholders unless the ticket allows it
- Never use any — validate all inputs with Zod
- Server Components by default; Server Actions for mutations; auth check in every action
- Business logic in features/<name>/lib/, not in UI components or route files
- No cross-feature imports — compose in app/ layer
- If requirements are ambiguous: STOP and ask — do not guess

## During execution

- Set ticket status to in_progress
- Minimal diff — no drive-by refactors
- Explain risks before large changes (10+ files, schema changes, shared utilities)
- Log bugs outside scope in KNOWN_ISSUES.md — do not fix them
- Log architectural choices in DECISIONS_LOG.md

## After implementation

- Run lint, typecheck, and tests
- Self-review every acceptance criterion in the ticket
- Set ticket status to review
- Update PROJECT_CONTEXT, DECISIONS_LOG, or KNOWN_ISSUES only if this ticket requires it
- Do not modify ai/project/product/ files

## Completion report

Ticket: TICKET-NNN
Status: review
Files changed: [created | modified | deleted — list each]
Acceptance criteria: [all met | list unmet]
Tests: lint [pass/fail], typecheck [pass/fail], test [pass/fail]
Notes: [decisions made, deviations, blockers]
```

---

## Bugfix Ticket

Use for `type: bugfix`. Omit architecture deep-read unless the fix spans features.

```
Execute @ai/project/tickets/TICKET-NNN-your-slug.md

You are a ticket executor. Fix only what this ticket specifies.

## Before writing any code — read

1. The ticket — verify status and blocked_by dependencies
2. ai/reusable/workflow/EXECUTION_RULES.md
3. ai/reusable/runtime/AGENT_RULES.md
4. ai/project/memory/KNOWN_ISSUES.md — check if this bug is already logged
5. ai/project/memory/PROJECT_CONTEXT.md
6. ai/reusable/stack/TECH_RULES.md
7. ai/reusable/stack/PATTERNS.md — if fix involves Server Actions or forms
8. All files listed in ticket.files — read fully before changing

## Hard constraints

- Minimal fix only — no unrelated refactors
- Only modify files listed in the ticket
- If root cause is outside scope: STOP and report; do not expand scope silently
- If bug was in KNOWN_ISSUES.md: move to Resolved after fix

## After implementation

- Run lint, typecheck, and tests
- Set ticket status to review
- Report: files changed, AC met, whether KNOWN_ISSUES was updated
```

---

## Refactor Ticket

Use for `type: refactor` — restructure without behavior change.

```
Execute @ai/project/tickets/TICKET-NNN-your-slug.md

You are a ticket executor. Refactor only what this ticket specifies. Behavior must not change unless the ticket says so.

## Before writing any code — read

1. The ticket
2. ai/reusable/workflow/EXECUTION_RULES.md
3. ai/reusable/runtime/AGENT_RULES.md
4. ai/reusable/stack/ARCHITECTURE.md
5. ai/project/memory/DECISIONS_LOG.md — check for constraints on this area
6. ai/project/memory/PROJECT_CONTEXT.md
7. Every file in ticket.files — read fully before changing

## Hard constraints

- No behavior changes unless explicitly in scope.in_scope
- Only files listed in the ticket
- No scope expansion — if more files need changes, STOP

## After implementation

- Run lint, typecheck, and tests (behavior unchanged)
- Set ticket status to review
- Report: files changed, AC met, any DECISIONS_LOG updates
```

---

## Chore Ticket

Use for `type: chore` — tooling, deps, config.

```
Execute @ai/project/tickets/TICKET-NNN-your-slug.md

You are a ticket executor. Complete only what this ticket specifies.

## Before writing any code — read

1. The ticket
2. ai/reusable/workflow/EXECUTION_RULES.md
3. ai/project/memory/PROJECT_CONTEXT.md
4. ai/reusable/stack/DEPENDENCIES.md — if adding or updating packages
5. ai/project/memory/DECISIONS_LOG.md — if making tooling or dependency choices

## Hard constraints

- New dependencies require DECISIONS_LOG entry per DEPENDENCIES.md
- Only files listed in the ticket

## After implementation

- Run lint, typecheck, and tests
- Set ticket status to review
- Report: files changed, AC met
```

---

## Review Ticket (no code)

Use when you want the agent to validate a ticket before implementation.

```
Review @ai/project/tickets/TICKET-NNN-your-slug.md against ai/reusable/workflow/TICKET_SCHEMA.md.

Do not write code.

Check:
- All required fields present
- scope.in_scope and scope.out_of_scope are both non-empty
- files lists every file that will be touched
- acceptance_criteria are testable
- blocked_by tickets are done (or flag if not)
- Safe to run in parallel with other in-progress tickets (see PARALLELISM_RULES.md)

Report: ready | not ready — list every gap.
If ready, say "Set status to ready and use TICKET_EXECUTION_PROMPT to implement."
```

---

## SYSTEM_PROMPT vs This File

| File | Purpose |
|------|---------|
| [SYSTEM_PROMPT.md](./SYSTEM_PROMPT.md) | Standing agent identity — custom instructions, external tools |
| **TICKET_EXECUTION_PROMPT.md** | Per-ticket chat message — full read list + constraints for one run |

Use both: system prompt sets identity; this prompt starts each ticket with explicit context loading.

---

## Related

- [EXECUTION_RULES.md](../workflow/EXECUTION_RULES.md) — detailed execution protocol
- [CONTEXT_POLICY.md](./CONTEXT_POLICY.md) — authoritative read order by ticket type
- [FAILURE_MODES.md](./FAILURE_MODES.md) — what to do when drift is detected
- [TICKET_SCHEMA.md](../workflow/TICKET_SCHEMA.md) — required ticket fields
