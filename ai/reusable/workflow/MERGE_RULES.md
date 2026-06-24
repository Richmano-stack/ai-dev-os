> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Merge Rules

Branch management, pull request requirements, and post-merge procedures.

---

## Branch Naming

```
ticket/TICKET-NNN-short-slug
```

Examples:

- `ticket/TICKET-003-create-task-action`
- `ticket/TICKET-015-fix-auth-redirect`
- `ticket/TICKET-022-add-task-filters`

**Rules:**

- One branch per ticket
- Branch from `main` (or the project's primary branch)
- Do not reuse branches across tickets
- Do not commit unrelated work to a ticket branch

---

## Pull Request Requirements

Every PR must include:

### Title

```
[TICKET-NNN] Short description matching ticket title
```

### Body Template

```markdown
## Ticket

TICKET-NNN: [ticket title]

## Summary

[1-3 sentences: what this PR does]

## Files Changed

| Action | File |
|--------|------|
| create | path/to/file |
| modify | path/to/file |

## Acceptance Criteria

- [x] Criterion 1
- [x] Criterion 2
- [ ] Criterion 3 (if not yet met — PR should not be opened)

## Test Plan

- [ ] Lint passes
- [ ] Type check passes
- [ ] Unit tests pass
- [ ] Manual verification steps (if applicable)

## Risks

[None | description of risks and mitigations]
```

### PR Checklist (reviewer)

- [ ] PR title includes ticket ID
- [ ] All acceptance criteria are checked
- [ ] Only ticket-scoped files are changed
- [ ] No `any` types introduced
- [ ] Zod validation on all new input boundaries
- [ ] Auth checks on all new mutations
- [ ] Tests included for new logic
- [ ] No mock implementations in production paths
- [ ] No secrets or env values in the diff

---

## Merge Order

When multiple tickets are ready to merge:

1. **Schema and migration tickets** first
2. **Shared infrastructure** (lib, auth helpers)
3. **Tickets in `blocked_by` dependency order**
4. **Independent tickets** in any order

If unsure, merge sequentially and run CI between each merge.

---

## Conflict Resolution

### File Conflicts

1. Identify which tickets caused the conflict.
2. Merge the earlier ticket (per dependency order) first.
3. Rebase the later ticket's branch onto updated `main`.
4. Resolve conflicts preserving both tickets' intent.
5. Re-run CI on the rebased branch.

### Schema Conflicts

Database migrations must not conflict. If they do:

1. Merge the first migration.
2. Renumber or rebase the second migration.
3. Verify both migrations apply cleanly in sequence.

### Type Contract Conflicts

If two tickets modified the same exported type:

1. Merge the ticket that **defines** the type first.
2. Update the consuming ticket to match the merged type.
3. Re-run typecheck.

---

## Merge Strategy

- **Squash merge** for feature tickets (one commit per ticket on `main`).
- **Merge commit** only when preserving history is explicitly needed (rare).

After squash merge, the commit message should be:

```
[TICKET-NNN] Ticket title (#PR-number)
```

---

## Post-Merge

After a PR is merged:

### 1. Update Ticket

Set ticket status to `done`.

### 2. Update Memory

| Condition | Update |
|-----------|--------|
| Architectural decision was made | Add entry to `ai/project/memory/DECISIONS_LOG.md` |
| Bug was fixed | Move issue to Resolved in `ai/project/memory/KNOWN_ISSUES.md` |
| New bug or debt discovered | Add to `ai/project/memory/KNOWN_ISSUES.md` |
| Repo structure changed | Update `ai/project/memory/PROJECT_CONTEXT.md` |

### 3. Unblock Dependent Tickets

Check if any tickets have `blocked_by: TICKET-NNN`. If all their blockers are now `done`, set them to `ready`.

### 4. Delete Branch

Delete the remote and local ticket branch after merge.

---

## CI Requirements

All of the following must pass before merge:

| Check | Command |
|-------|---------|
| Lint | `npm run lint` |
| Type check | `npm run typecheck` |
| Unit tests | `npm run test` |
| Build | `npm run build` |

E2E tests (`npm run test:e2e`) are required for tickets touching critical user flows (auth, payments, data mutations).

---

## Hotfix Process

For production-critical fixes:

1. Create ticket with `type: bugfix` and `priority: P0`.
2. Branch from `main`: `ticket/TICKET-NNN-hotfix-description`.
3. Minimal fix — no refactoring.
4. Expedited review allowed, but review is still required.
5. Merge to `main` and deploy.
6. Post-merge: update KNOWN_ISSUES if the bug was logged there.

---

## Revert Policy

If a merged ticket causes production issues:

1. Revert the merge commit on `main`.
2. Set ticket status back to `in_progress`.
3. Log the issue in `KNOWN_ISSUES.md`.
4. Fix on the original ticket branch and re-merge.

Do not force-push to `main`.
