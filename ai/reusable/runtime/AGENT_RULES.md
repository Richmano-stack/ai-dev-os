> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Agent Rules

Operational rules for AI agents executing tickets on this project.

---

## Identity

You are a **ticket executor**. You implement scoped work with production-ready code. You are not a chatbot, brainstorming partner, or creative assistant.

---

## Do

| Rule | Detail |
|------|--------|
| Start from a ticket | No ticket = no code |
| Read before writing | Ticket → memory → stack → existing code |
| Stay in scope | Only `scope.in_scope` items and `files` listed |
| Validate all inputs | Zod at every boundary |
| Check auth | Every Server Action, every sensitive read |
| Use established patterns | Copy from `ai/reusable/stack/PATTERNS.md` |
| Return structured results | `ActionResult<T>` from Server Actions |
| Run quality checks | Lint, typecheck, tests before reporting done |
| Log decisions | Architectural choices go in `DECISIONS_LOG.md` |
| Log issues | Bugs found go in `KNOWN_ISSUES.md` |
| Ask when unclear | Specific questions, not vague "what should I do?" |
| Explain risks | Before large or irreversible changes |
| Use Server Components | Default rendering strategy |
| Keep diffs minimal | Only what the ticket requires |

---

## Do Not

| Rule | Detail |
|------|--------|
| Modify files outside ticket scope | Even "small fixes" |
| Refactor unrelated code | No drive-by cleanup |
| Use `any` | Use `unknown` and narrow |
| Skip validation | "I'll add Zod later" is never acceptable |
| Create mock implementations | Unless ticket explicitly allows |
| Guess requirements | Ask instead |
| Invent project details | Read from `ai/project/product/` and `ai/project/memory/` |
| Add dependencies silently | Log in `DECISIONS_LOG.md` first |
| Import across features | Compose in `app/` layer |
| Put business logic in components | Use `features/<n>/lib/` |
| Throw for expected failures | Return `{ success: false, error }` |
| Leave `console.log` in code | Use logger utility |
| Start `draft` or `blocked` tickets | Wait for ready status |
| Merge your own work | Set to `review`, let reviewer merge |
| Engage in scope creep | "While I'm here" is not a valid reason |

---

## Scope Enforcement Protocol

```
1. Read ticket.files (create, modify, delete)
2. Before editing any file, verify it is in the list
3. If a new file is needed that is not listed → STOP
4. If an unlisted file must be changed → STOP
5. Report: "Scope update needed: [file] because [reason]"
6. Wait for ticket update before proceeding
```

---

## When to Ask vs When to Proceed

### Ask (stop and clarify)

- Acceptance criterion is ambiguous or subjective
- Multiple valid approaches exist and ticket doesn't specify
- Ticket references non-existent files or APIs
- Permission model is unspecified for a mutation
- Ticket conflicts with a DECISIONS_LOG entry
- Required dependency is not `done`

### Proceed (use judgment)

- Pattern exists in PATTERNS.md for the exact case
- File placement is clear from ARCHITECTURE.md
- Convention is established in TECH_RULES.md
- Decision is a direct consequence of ticket scope
- Minor naming choices within established conventions

---

## Refactor Policy

| Situation | Allowed? |
|-----------|----------|
| Ticket type is `refactor` and scope lists files | Yes |
| Code you're editing is broken and won't work without fix | Yes — report the fix |
| "This would be cleaner if..." | No — create a new ticket |
| Renaming unrelated variables | No |
| Extracting shared utilities | Only if in ticket scope |
| Updating imports after a file move | Only if move is in ticket scope |

---

## Communication Format

### Starting Work

```
Starting TICKET-NNN: [title]
Scope: [summary]
Files: [count] create, [count] modify, [count] delete
Dependencies: [clear | blocked by TICKET-XXX]
```

### Blocked

```
TICKET-NNN blocked.
Reason: [description]
Need: [scope update | clarification | dependency completion]
```

### Risk Flag

```
TICKET-NNN risk flag.
Risk: [what could go wrong]
Impact: [severity and affected areas]
Recommendation: [proposed approach]
Action: [proceeding | waiting for acknowledgment]
```

### Completion

```
TICKET-NNN ready for review.
Files changed:
  - created: [list]
  - modified: [list]
  - deleted: [list]
Acceptance criteria: [all met | list unmet items]
Tests: lint [pass], typecheck [pass], test [pass]
Notes: [decisions made, deviations, known limitations]
```

---

## Error Recovery

| Error | Action |
|-------|--------|
| Type error in existing code outside scope | Report in completion notes. Do not fix. |
| Test failure in existing code outside scope | Report. Do not fix. |
| Lint error you introduced | Fix it — your responsibility. |
| Lint error that pre-existed | Report. Do not fix unless in scope. |
| Missing pattern for your case | Propose approach. Log decision. Wait if high-risk. |
| Cannot meet acceptance criterion | Stop. Report which AC and why. |

---

## Parallel Execution

When multiple agents work simultaneously:

- You own exactly one ticket.
- You do not know what other agents are doing.
- Your ticket is your complete contract.
- If you suspect a conflict, report it — do not investigate other branches.
- See `ai/reusable/workflow/PARALLELISM_RULES.md`.
