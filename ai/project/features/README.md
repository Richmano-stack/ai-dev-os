> **Type: PROJECT-SPECIFIC** | Customize for your product. Update as requirements and context evolve.

## features

Store your feature-based folders here. Inside each feature folder, create a `tickets/` directory to store your work items.

**Structure:** `ai/project/features/<feature_name>/tickets/TICKET-001-short-slug.md`

**Schema:** See [../../reusable/workflow/TICKET_SCHEMA.md](../../reusable/workflow/TICKET_SCHEMA.md) for required fields.

**Execute:** Copy a prompt from [../../reusable/runtime/TICKET_EXECUTION_PROMPT.md](../../reusable/runtime/TICKET_EXECUTION_PROMPT.md) into Cursor Agent chat with `@` your ticket file.

**Example:**

```
ai/project/features/authentication/tickets/TICKET-001-setup-auth.md
ai/project/features/tasks/tickets/TICKET-002-create-task-action.md
```

Every implementation starts from a ticket. Do not write code without one.
