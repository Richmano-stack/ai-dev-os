> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Technical Rules

Hard constraints for all code in this project. Violations must be caught in review and fixed before merge.

---

## TypeScript

### Strict Mode (required)

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true
  }
}
```

### Type Rules

| Rule | Enforcement |
|------|-------------|
| No `any` | Use `unknown` and narrow. ESLint `@typescript-eslint/no-explicit-any: error`. |
| No `@ts-ignore` | Fix the type error. `@ts-expect-error` only with a comment explaining why. |
| Explicit return types on exports | All exported functions and Server Actions must declare return type. |
| Prefer `satisfies` over `as` | `const config = { ... } satisfies Config` not `as Config`. |
| Derive types from Zod | `type Input = z.infer<typeof schema>` — never duplicate shapes. |
| No non-null assertions (`!`) | Use proper null checks or optional chaining. |

```typescript
// Bad
const task = tasks.find((t) => t.id === id)!;

// Good
const task = tasks.find((t) => t.id === id);
if (!task) {
  return { success: false, error: "Task not found" };
}
```

---

## Validation

**Every external input boundary must be validated with Zod.**

| Boundary | Schema Location | Example |
|----------|-----------------|---------|
| Server Actions | `features/<n>/schemas/` | Form data, action params |
| API Route Handlers | `features/<n>/schemas/` or `lib/schemas/` | Webhook payloads |
| Environment variables | `lib/env.ts` | Startup validation |
| URL search params | `features/<n>/schemas/` | Pagination, filters |

```typescript
// Required pattern for Server Actions
const parsed = inputSchema.safeParse(rawInput);
if (!parsed.success) {
  return { success: false, error: "Invalid input" };
}
// Use parsed.data from here — never rawInput
```

Never trust client-side validation alone. Zod on the server is mandatory even if the client also validates.

---

## Forms

Use React Hook Form with Zod resolver for all user-facing forms.

```tsx
"use client";

import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { createTaskSchema, type CreateTaskInput } from "../schemas/task.schema";
import { createTask } from "../actions/create-task";

export function TaskForm() {
  const form = useForm<CreateTaskInput>({
    resolver: zodResolver(createTaskSchema),
    defaultValues: { title: "", assigneeId: "" },
  });

  async function onSubmit(data: CreateTaskInput) {
    const result = await createTask(data);
    if (!result.success) {
      form.setError("root", { message: result.error });
      return;
    }
    form.reset();
  }

  return (
    <form onSubmit={form.handleSubmit(onSubmit)}>
      {/* fields */}
    </form>
  );
}
```

**Rules:**

- Form component is a Client Component.
- Schema is shared between form resolver and Server Action.
- Display server errors via `form.setError("root", ...)`.
- Never submit unvalidated form data to Server Actions.

---

## Error Handling

### Server Actions

Return structured results. Do not throw for expected failures.

```typescript
type ActionResult<T> =
  | { success: true; data: T }
  | { success: false; error: string };

// Expected failures: return error result
if (!user) {
  return { success: false, error: "Unauthorized" };
}

// Unexpected failures: let error boundary catch
// Only throw for truly exceptional system errors
```

### User-Facing Errors

- Messages must be safe to display (no stack traces, no internal IDs).
- Log detailed errors server-side.
- Use `error.tsx` boundaries for unrecoverable page errors.

### Error Logging

```typescript
// lib/logger.ts
export function logError(context: string, error: unknown) {
  console.error(`[${context}]`, error instanceof Error ? error.message : error);
}
```

---

## Security

| Rule | Detail |
|------|--------|
| Auth in Server Actions | Check session/permissions before every mutation and sensitive read. |
| No secrets in client | Never pass API keys, tokens, or secrets to Client Components. |
| No `NEXT_PUBLIC_` for secrets | Only truly public values (e.g., analytics ID). |
| CSRF | Server Actions have built-in CSRF protection. Do not disable. |
| SQL injection | Use ORM parameterized queries. Never interpolate user input into SQL. |
| XSS | React escapes by default. Never use `dangerouslySetInnerHTML` without sanitization. |
| Rate limiting | Apply to auth endpoints and public API routes. |

```typescript
// Required auth check pattern
export async function deleteTask(taskId: string): Promise<ActionResult<void>> {
  const session = await getSession();
  if (!session?.user) {
    return { success: false, error: "Unauthorized" };
  }

  const task = await getTaskById(taskId);
  if (!task) {
    return { success: false, error: "Task not found" };
  }

  if (task.assigneeId !== session.user.id && !session.user.isAdmin) {
    return { success: false, error: "Forbidden" };
  }

  await db.task.delete({ where: { id: taskId } });
  revalidatePath("/tasks");
  return { success: true, data: undefined };
}
```

---

## Server vs Client Boundary

| Concern | Server | Client |
|---------|--------|--------|
| Database access | Yes | Never |
| Environment secrets | Yes | Never |
| `useState`, `useEffect` | Never | Yes |
| Event handlers | Never | Yes |
| Data fetching (initial) | Yes | Never |
| Form interactivity | Never | Yes |

**Violation signal:** `import { db }` in a file with `"use client"` — this is always wrong.

---

## Testing

| Layer | Tool | What to Test |
|-------|------|--------------|
| Business logic | Vitest | `features/<n>/lib/` functions |
| Server Actions | Vitest | Input validation, auth checks, return shapes |
| Components | Testing Library | User interactions, accessibility |
| Critical flows | Playwright | Auth, CRUD, payment |

**Rules:**

- Test behavior, not implementation details.
- Every Server Action with auth logic must have an unauthorized test case.
- Every Zod schema must have valid and invalid input test cases.
- No snapshot tests for components (too brittle).

---

## Accessibility

- All interactive elements must be keyboard accessible.
- Form inputs must have associated labels.
- Images must have `alt` text.
- Color contrast must meet WCAG 2.1 AA.
- Use semantic HTML (`button`, `nav`, `main`, `article`).
- shadcn/ui components handle most a11y by default — do not strip ARIA attributes.

---

## Performance

- Images: use `next/image` with explicit `width` and `height`.
- Fonts: use `next/font` — no external font `<link>` tags.
- Bundle: check that heavy libraries are not imported in Client Components.
- Database: add indexes for columns used in `WHERE` and `ORDER BY`.
- Avoid N+1 queries — use ORM `include` or batch queries.

---

## Code Style

- Use `const` by default. `let` only when reassignment is necessary. Never `var`.
- Prefer named exports over default exports (except Next.js page/layout files).
- File names: kebab-case.
- Component names: PascalCase.
- Max function length: ~40 lines. Extract helpers when exceeded.
- No commented-out code in commits.
- No `console.log` in production code — use the logger utility.

---

## Git and Code Review

- Every change must trace to a ticket.
- PRs must pass lint, type check, and tests before merge.
- No drive-by refactors in feature PRs.
- New dependencies require approval (see DEPENDENCIES.md).
