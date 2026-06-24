> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Ticket Schema

Every unit of work is a ticket. Tickets are the contract between product intent and implementation.

---

## Required Fields

```yaml
id: TICKET-001
title: "Short, descriptive title"
type: feature | bugfix | refactor | chore
status: draft | ready | in_progress | blocked | review | done
priority: P0 | P1 | P2 | P3

scope:
  summary: "One paragraph describing what this ticket accomplishes"
  in_scope:
    - "Specific capability or change included"
  out_of_scope:
    - "Specific capability or change explicitly excluded"

files:
  create:
    - "path/to/new-file.ts"
  modify:
    - "path/to/existing-file.ts"
  delete:
    - "path/to/removed-file.ts"

dependencies:
  blocks:
    - "TICKET-xxx"
  blocked_by:
    - "TICKET-xxx"
  related:
    - "TICKET-xxx"

acceptance_criteria:
  - "Testable criterion 1"
  - "Testable criterion 2"

definition_of_done:
  - "Lint passes"
  - "Type check passes"
  - "Tests pass"
  - "Only scoped files modified"

risks:
  - "Description of risk and mitigation"

notes: ""
```

---

## Field Reference

### `id`

Format: `TICKET-NNN` (zero-padded three digits).

Assign sequentially. IDs are immutable.

### `title`

Imperative mood, under 80 characters.

- Good: "Add task status update Server Action"
- Bad: "Task status stuff"

### `type`

| Type | When to Use |
|------|-------------|
| `feature` | New user-facing capability |
| `bugfix` | Fix broken behavior |
| `refactor` | Restructure without behavior change |
| `chore` | Tooling, deps, config (no user impact) |

### `status`

See status flow in [WORKFLOW.md](./WORKFLOW.md).

### `priority`

| Priority | Meaning |
|----------|---------|
| `P0` | Critical — blocks release or users |
| `P1` | High — current phase requirement |
| `P2` | Medium — should have, not blocking |
| `P3` | Low — nice to have |

### `scope`

**The most important section.** AI agents use this to enforce boundaries.

- `summary`: What and why in 1-3 sentences.
- `in_scope`: Explicit list of what this ticket delivers.
- `out_of_scope`: Explicit list of what this ticket does NOT deliver, even if related.

If a task is not in `in_scope`, the executor must not do it.

### `files`

List every file expected to be touched. AI agents must not modify files not listed here.

- `create`: New files to add.
- `modify`: Existing files to change.
- `delete`: Files to remove.

If unanticipated files need changes during execution, **stop and update the ticket** before proceeding.

### `dependencies`

- `blocks`: Tickets that cannot start until this one is done.
- `blocked_by`: Tickets that must be done before this one starts.
- `related`: Tickets that share context but have no hard dependency.

### `acceptance_criteria`

Testable conditions that must all be true for the ticket to be `done`.

- Write as checkboxes.
- Each criterion must be verifiable — no subjective language.
- Bad: "Code looks clean"
- Good: "createTask Server Action returns error when title is empty"

### `definition_of_done`

Standard checklist applied to every ticket. Customize only when the ticket has additional requirements.

### `risks`

Known risks and mitigations. Helps reviewers and parallel execution checks.

---

## Ticket File Location

Store tickets as Markdown files:

```
ai/project/tickets/TICKET-001-short-slug.md
```

Or in your project management tool — but the fields above are required regardless of storage.

---

## Worked Example

```yaml
id: TICKET-003
title: "Add create task Server Action and form"
type: feature
status: ready
priority: P1

scope:
  summary: >
    Implement the create task flow: a Server Action that validates input,
    persists to the database, and a form component that calls the action.
    This is the first write operation for the tasks feature.
  in_scope:
    - Zod schema for create task input
    - createTask Server Action with auth check
    - CreateTaskForm client component with React Hook Form
    - Wire form into the tasks page
  out_of_scope:
    - Task editing or deletion
    - Email notifications on task creation
    - Optimistic UI updates
    - File attachments on tasks

files:
  create:
    - src/features/tasks/schemas/task.schema.ts
    - src/features/tasks/actions/create-task.ts
    - src/features/tasks/components/create-task-form.tsx
    - src/features/tasks/types/task.types.ts
  modify:
    - src/app/(dashboard)/tasks/page.tsx
    - src/features/tasks/index.ts
  delete: []

dependencies:
  blocks:
    - TICKET-004
  blocked_by:
    - TICKET-001
  related:
    - TICKET-002

acceptance_criteria:
  - "createTask action validates input with Zod and returns error for invalid data"
  - "createTask action requires authenticated session"
  - "createTask action persists task to database and returns task ID"
  - "createTask action calls revalidatePath after success"
  - "CreateTaskForm shows validation errors inline"
  - "CreateTaskForm shows server error on action failure"
  - "CreateTaskForm resets on successful submission"
  - "Tasks page renders CreateTaskForm above the task list"

definition_of_done:
  - "Lint passes (npm run lint)"
  - "Type check passes (npm run typecheck)"
  - "Unit tests for createTask action (valid, invalid, unauthorized)"
  - "Only files listed in ticket were modified"
  - "No mock implementations"

risks:
  - "Database schema for tasks table must exist (TICKET-001) — blocked_by handles this"
  - "Auth session helper must be available — verify in PROJECT_CONTEXT.md"

notes: "Follow patterns in ai/reusable/stack/PATTERNS.md sections 1 and 2"
```

---

## Creating a Ticket from a User Story

1. Find the user story in `ai/project/product/USER_STORIES.md`.
2. Verify it is in scope per `ai/project/product/SCOPE.md`.
3. Break the story into one or more tickets if it spans multiple concerns.
4. Map each acceptance criterion from the story to the ticket.
5. List all files based on architecture in `ai/reusable/stack/ARCHITECTURE.md`.
6. Check dependencies against existing tickets.
7. Set status to `ready`.

---

## Ticket Quality Checklist

Before setting status to `ready`:

- [ ] `scope.in_scope` and `scope.out_of_scope` are both non-empty
- [ ] `files` lists every file that will be touched
- [ ] `acceptance_criteria` are all testable
- [ ] `dependencies` are checked against existing tickets
- [ ] `blocked_by` tickets are `done` or scheduled first
- [ ] Parallelism check will be run before execution
