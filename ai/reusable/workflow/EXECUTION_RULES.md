> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Execution Rules

Rules for the executor (human developer or AI agent) during ticket implementation.

---

## Identity

You are a **ticket executor**, not a chatbot or creative assistant. Your job is to implement exactly what the ticket specifies, following project standards, and nothing more.

---

## Pre-Execution

Before writing any code:

### 1. Read the Ticket

- [ ] Ticket status is `ready` or `in_progress`
- [ ] All required fields are populated (see [TICKET_SCHEMA.md](./TICKET_SCHEMA.md))
- [ ] `blocked_by` tickets are `done`
- [ ] You understand every acceptance criterion

### 2. Read Project Context

- [ ] `ai/project/memory/PROJECT_CONTEXT.md` — repo structure, env vars, integrations
- [ ] `ai/project/memory/KNOWN_ISSUES.md` — active bugs and workarounds
- [ ] `ai/project/memory/DECISIONS_LOG.md` — if ticket touches architectural areas

### 3. Read Stack Standards

- [ ] `ai/reusable/stack/TECH_RULES.md` — hard constraints
- [ ] `ai/reusable/stack/PATTERNS.md` — implementation patterns to follow
- [ ] `ai/reusable/stack/ARCHITECTURE.md` — where files belong

### 4. Run Parallelism Check

- [ ] Completed checklist in [PARALLELISM_RULES.md](./PARALLELISM_RULES.md)
- [ ] No conflicts with in-progress tickets

### 5. Set Ticket Status

Update ticket status to `in_progress`.

---

## During Execution

### Scope Discipline

| Rule | Detail |
|------|--------|
| Stay in scope | Only implement items listed in `scope.in_scope` |
| Respect out-of-scope | Do not implement items listed in `scope.out_of_scope` |
| File boundary | Only create, modify, or delete files listed in `files` |
| No drive-by refactors | Do not "improve" code outside the ticket |
| No new dependencies | Without logging in DECISIONS_LOG |
| No mock implementations | Unless ticket explicitly allows it |

### When Files Outside Scope Need Changes

1. **Stop implementation.**
2. Report which files need changes and why.
3. Wait for ticket scope update.
4. Do not proceed until the ticket author adds the files.

### When Requirements Are Missing

1. **Stop implementation.**
2. List specific ambiguities.
3. Ask for clarification.
4. Do not guess or assume.

Examples of when to ask:

- Acceptance criterion is subjective ("make it look good")
- Multiple valid implementations exist and ticket doesn't specify
- Ticket references a file or API that doesn't exist
- Auth/permission rules are not specified for a mutation

### Implementation Standards

- Follow patterns in `ai/reusable/stack/PATTERNS.md` — copy and adapt, don't invent.
- Validate all inputs with Zod.
- Check auth in every Server Action.
- Server Components by default.
- Business logic in `features/<n>/lib/`, not in components or route files.
- Return `ActionResult` from Server Actions, don't throw for expected failures.

### Large Changes

Before making a change that:

- Touches more than 10 files
- Modifies a shared utility used by 3+ features
- Changes a database schema
- Removes an existing API or action

**Stop and explain the risk** to the reviewer. Wait for acknowledgment before proceeding.

### Discovering Issues

| Discovery | Action |
|-----------|--------|
| Bug outside ticket scope | Log in `KNOWN_ISSUES.md`. Do not fix. |
| Architectural decision needed | Log in `DECISIONS_LOG.md`. Proceed if within scope. |
| Ticket is wrong or incomplete | Stop. Request ticket update. |
| Pattern doesn't cover the case | Propose approach. Log decision. Wait if high-risk. |

---

## Post-Execution

Before setting ticket to `review`:

### 1. Self-Review Against Acceptance Criteria

Go through each criterion in the ticket. Verify it is met. Check them off.

### 2. Self-Review Against Scope

- [ ] Only files listed in `files` were touched
- [ ] No unrelated changes in the diff
- [ ] No commented-out code
- [ ] No `console.log` left in production code
- [ ] No `any` types introduced
- [ ] No `TODO` comments (unless ticket allows)

### 3. Run Quality Checks

```bash
npm run lint
npm run typecheck
npm run test
```

All must pass. Fix failures before proceeding.

### 4. Update Ticket

- Set status to `review`
- Add notes if anything deviated from plan (with justification)

---

## Stop Conditions

Stop immediately and report when:

| Condition | Action |
|-----------|--------|
| Scope creep detected | Stop. Report extra work needed. |
| Missing acceptance criteria | Stop. Ask for clarification. |
| Blocked dependency discovered | Set status to `blocked`. Report. |
| Merge conflict with in-progress ticket | Stop. Check parallelism rules. |
| Test failures you cannot resolve | Stop. Report failure details. |
| Architectural change required outside scope | Stop. Propose new ticket. |

---

## Communication Format

### Status Update

```
Ticket: TICKET-NNN
Status: in_progress | blocked | review
Progress: [what is done]
Remaining: [what is left]
Blockers: [none | description]
```

### Risk Flag

```
Ticket: TICKET-NNN
Risk: [description]
Impact: [what could go wrong]
Recommendation: [proposed approach]
Awaiting: [acknowledgment | clarification | scope update]
```

### Completion Report

```
Ticket: TICKET-NNN
Status: review
Files changed: [list]
AC met: [all | list of unmet]
Tests: [pass | fail details]
Notes: [deviations or decisions made]
```

---

## What Not to Do

- Do not refactor code outside the ticket "while you're there"
- Do not add features not in `scope.in_scope`
- Do not skip validation "for now"
- Do not use `any` to move faster
- Do not create mock/stub implementations to unblock yourself
- Do not modify `ai/project/product/` files (those are human-authored)
- Do not merge your own PR without review
- Do not start a ticket that is `draft` or `blocked`
