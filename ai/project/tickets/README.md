> **Type: PROJECT-SPECIFIC** | Customize for your product. Update as requirements and context evolve.

## tickets

Store ticket files here — one Markdown file per ticket.

**Naming:** `TICKET-001-short-slug.md`

**Schema:** See [../../reusable/workflow/TICKET_SCHEMA.md](../../reusable/workflow/TICKET_SCHEMA.md) for required fields.

**Execute:** Copy a prompt from [../../reusable/runtime/TICKET_EXECUTION_PROMPT.md](../../reusable/runtime/TICKET_EXECUTION_PROMPT.md) into Cursor Agent chat with `@` your ticket file.

**Example:**

```
ai/project/tickets/TICKET-001-setup-auth.md
ai/project/tickets/TICKET-002-create-task-action.md
```

Every implementation starts from a ticket. Do not write code without one.
