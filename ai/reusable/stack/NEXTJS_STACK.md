> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Next.js Stack

Standard technology choices for projects using this AI Dev OS. All agents and developers must follow these defaults unless a ticket explicitly overrides them with a logged decision.

---

## Core Stack

| Technology | Role | Notes |
|------------|------|-------|
| **Next.js** (App Router) | Framework | Use `src/` directory. No Pages Router. |
| **TypeScript** | Language | `strict: true` in `tsconfig.json`. No `any`. |
| **React** | UI | Server Components by default. React 19 compatible patterns. |
| **Zod** | Validation | All external input boundaries. Single source of truth for shapes. |
| **React Hook Form** | Forms | With `@hookform/resolvers/zod`. Client-side UX only. |
| **Server Actions** | Mutations | Preferred over API Route Handlers for all CRUD. |
| **Tailwind CSS** | Styling | Utility-first. Use `cn()` helper for conditional classes. |

---

## Optional Approved Additions

| Technology | When to Use |
|------------|-------------|
| **Prisma** or **Drizzle** | Database ORM. Pick one per project; log in DECISIONS_LOG. |
| **next-auth** (Auth.js) | Authentication when OAuth/session management is needed. |
| **nuqs** | URL search param state for filters, pagination, sorting. |
| **shadcn/ui** | Accessible UI primitives in `src/components/ui/`. |
| **Vitest** + **Testing Library** | Unit and component tests. |
| **Playwright** | E2E tests for critical user flows. |

---

## Directory Conventions

```
src/
├── app/                    # Routes, layouts, loading/error boundaries
│   ├── (auth)/             # Route groups for layout isolation
│   ├── (dashboard)/
│   ├── layout.tsx
│   ├── page.tsx
│   ├── loading.tsx
│   └── error.tsx
├── features/               # Feature modules (primary code organization)
│   └── <feature>/
│       ├── actions/        # Server Actions
│       ├── components/     # Feature-specific components
│       ├── schemas/        # Zod schemas
│       ├── types/          # Feature types
│       ├── lib/            # Feature business logic
│       └── index.ts        # Public API (barrel export)
├── components/
│   └── ui/                 # Shared UI primitives (shadcn)
└── lib/                    # Shared infrastructure
    ├── db/                 # Database client
    ├── env.ts              # Validated environment variables
    └── utils.ts            # Shared utilities (cn, etc.)
```

**Rules:**

- Route files in `app/` are thin. They compose feature components and pass data.
- Business logic lives in `features/<name>/lib/`, never in `app/` route files.
- Shared UI primitives go in `components/ui/`. Feature components stay in features.

---

## Rendering Rules

### Server Components (default)

Every component is a Server Component unless it requires client capabilities.

Use Server Components for:

- Data fetching
- Accessing backend resources (database, env vars)
- Keeping large dependencies off the client bundle
- Static or cacheable content

### Client Components (`"use client"`)

Add `"use client"` only when the component needs:

- Event handlers (`onClick`, `onChange`, `onSubmit`)
- Browser APIs (`window`, `localStorage`, `IntersectionObserver`)
- Client hooks such as `useState`, `useTransition`, or `useRef`
- Third-party libraries that use hooks internally

**`useEffect` is a last resort** — see [useEffect policy](#useeffect) and TECH_RULES.md.

**Push `"use client"` to leaf components.** Do not mark entire page trees as client.

```tsx
// Good: client boundary at the interactive leaf
// app/dashboard/page.tsx (Server Component)
import { TaskList } from "@/features/tasks/components/task-list";

export default async function DashboardPage() {
  const tasks = await getTasks();
  return <TaskList initialTasks={tasks} />;
}
```

### Route Segment Files

| File | Purpose |
|------|---------|
| `layout.tsx` | Shared UI wrapper for a route segment |
| `page.tsx` | Unique UI for a route |
| `loading.tsx` | Suspense fallback for the segment |
| `error.tsx` | Error boundary for the segment |
| `not-found.tsx` | 404 UI for the segment |

Every route group with async data fetching should have `loading.tsx` and `error.tsx`.

### useEffect

Do not use `useEffect` unless no other approach works. Prefer Server Components for data, event handlers for user actions, derived values during render, `key` for resets, React Hook Form for forms, and `nuqs` for URL state. Full policy: TECH_RULES.md useEffect section. Copy-paste alternatives: PATTERNS.md section 9.

---

## Data Fetching

1. **Server-first.** Fetch data in Server Components or Server Actions.
2. **No `useEffect` for data.** Do not `useEffect` + `fetch` for initial page data or any data fetchable on the server or triggered by user action.
3. **Cache deliberately.** Use `fetch` cache options or `unstable_cache` with explicit revalidation strategy.
4. **Revalidate after mutations.** Call `revalidatePath` or `revalidateTag` in Server Actions after writes.

```typescript
// features/tasks/lib/queries.ts
import { db } from "@/lib/db";

export async function getTasksForUser(userId: string) {
  return db.task.findMany({
    where: { assigneeId: userId },
    orderBy: { updatedAt: "desc" },
  });
}
```

---

## Server Actions

- Place in `features/<name>/actions/`.
- Always validate input with Zod.
- Always check authorization before mutations.
- Return structured results, not thrown errors for expected failures.

```typescript
"use server";

import { z } from "zod";
import { revalidatePath } from "next/cache";

const updateTaskSchema = z.object({
  taskId: z.string().uuid(),
  status: z.enum(["todo", "in_progress", "done"]),
});

type ActionResult<T> =
  | { success: true; data: T }
  | { success: false; error: string };

export async function updateTaskStatus(
  input: z.infer<typeof updateTaskSchema>
): Promise<ActionResult<{ id: string }>> {
  const parsed = updateTaskSchema.safeParse(input);
  if (!parsed.success) {
    return { success: false, error: "Invalid input" };
  }

  // Auth check, business logic, DB write...

  revalidatePath("/dashboard");
  return { success: true, data: { id: parsed.data.taskId } };
}
```

---

## Environment and Secrets

- All env vars validated in `src/lib/env.ts` with Zod at startup.
- Never access `process.env` directly outside `env.ts`.
- Never expose secrets to client components or `NEXT_PUBLIC_` vars unless truly public.
- Maintain `.env.example` with all required variable names (no values).

```typescript
// src/lib/env.ts
import { z } from "zod";

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  NODE_ENV: z.enum(["development", "test", "production"]),
});

export const env = envSchema.parse(process.env);
```

---

## TypeScript Configuration

Required `tsconfig.json` settings:

```json
{
  "compilerOptions": {
    "strict": true,
    "noUncheckedIndexedAccess": true,
    "noImplicitReturns": true,
    "forceConsistentCasingInFileNames": true,
    "paths": {
      "@/*": ["./src/*"]
    }
  }
}
```

---

## What This Stack Does Not Include

- Pages Router
- CSS Modules or styled-components (use Tailwind)
- Redux or global client state libraries (use URL state + Server Components)
- Unvalidated `any` types
- Mock implementations in production code paths
