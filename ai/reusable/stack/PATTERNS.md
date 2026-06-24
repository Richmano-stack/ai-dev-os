> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Patterns

Concrete implementation patterns for this stack. Copy and adapt these — do not invent alternatives without logging a decision.

---

## 1. Server Action with Validation

```typescript
// features/tasks/actions/create-task.ts
"use server";

import { z } from "zod";
import { revalidatePath } from "next/cache";
import { createTaskSchema } from "../schemas/task.schema";
import { db } from "@/lib/db";
import { requireAuth } from "@/features/auth/lib/require-auth";

type ActionResult<T> =
  | { success: true; data: T }
  | { success: false; error: string };

export async function createTask(
  input: z.infer<typeof createTaskSchema>
): Promise<ActionResult<{ id: string }>> {
  const session = await requireAuth();

  const parsed = createTaskSchema.safeParse(input);
  if (!parsed.success) {
    return { success: false, error: "Invalid input" };
  }

  const task = await db.task.create({
    data: {
      title: parsed.data.title,
      assigneeId: parsed.data.assigneeId,
      createdById: session.user.id,
    },
  });

  revalidatePath("/tasks");
  return { success: true, data: { id: task.id } };
}
```

---

## 2. Form Wired to Server Action

```tsx
// features/tasks/components/create-task-form.tsx
"use client";

import { useForm } from "react-hook-form";
import { zodResolver } from "@hookform/resolvers/zod";
import { useTransition } from "react";
import { createTaskSchema, type CreateTaskInput } from "../schemas/task.schema";
import { createTask } from "../actions/create-task";
import { Button } from "@/components/ui/button";
import { Input } from "@/components/ui/input";
import { Label } from "@/components/ui/label";

export function CreateTaskForm() {
  const [isPending, startTransition] = useTransition();

  const form = useForm<CreateTaskInput>({
    resolver: zodResolver(createTaskSchema),
    defaultValues: { title: "", assigneeId: "" },
  });

  function onSubmit(data: CreateTaskInput) {
    startTransition(async () => {
      const result = await createTask(data);
      if (!result.success) {
        form.setError("root", { message: result.error });
        return;
      }
      form.reset();
    });
  }

  return (
    <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-4">
      <div>
        <Label htmlFor="title">Title</Label>
        <Input id="title" {...form.register("title")} />
        {form.formState.errors.title && (
          <p className="text-sm text-destructive">
            {form.formState.errors.title.message}
          </p>
        )}
      </div>

      {form.formState.errors.root && (
        <p className="text-sm text-destructive">
          {form.formState.errors.root.message}
        </p>
      )}

      <Button type="submit" disabled={isPending}>
        {isPending ? "Creating..." : "Create Task"}
      </Button>
    </form>
  );
}
```

---

## 3. Server Component Page with Data Fetching

```tsx
// app/(dashboard)/tasks/page.tsx
import { Suspense } from "react";
import { requireAuth } from "@/features/auth/lib/require-auth";
import { getTasksForUser } from "@/features/tasks/lib/queries";
import { TaskBoard } from "@/features/tasks/components/task-board";
import { CreateTaskForm } from "@/features/tasks/components/create-task-form";
import { TaskBoardSkeleton } from "@/features/tasks/components/task-board-skeleton";

export const metadata = { title: "Tasks" };

export default async function TasksPage() {
  const session = await requireAuth();

  return (
    <div className="space-y-6">
      <h1 className="text-2xl font-bold">Tasks</h1>
      <CreateTaskForm />
      <Suspense fallback={<TaskBoardSkeleton />}>
        <TaskList userId={session.user.id} />
      </Suspense>
    </div>
  );
}

async function TaskList({ userId }: { userId: string }) {
  const tasks = await getTasksForUser(userId);
  return <TaskBoard tasks={tasks} />;
}
```

---

## 4. Auth Guard for Server Actions

```typescript
// features/auth/lib/require-auth.ts
import { getSession } from "./session";
import { redirect } from "next/navigation";

export async function requireAuth() {
  const session = await getSession();
  if (!session?.user) {
    redirect("/sign-in");
  }
  return session;
}

// For Server Actions that return results instead of redirecting:
export async function getAuthSession() {
  const session = await getSession();
  if (!session?.user) {
    return null;
  }
  return session;
}
```

---

## 5. URL State for Filters and Pagination

```tsx
// app/(dashboard)/tasks/page.tsx
import { taskFilterSchema } from "@/features/tasks/schemas/task.schema";
import { getTasksForUser } from "@/features/tasks/lib/queries";

type TasksPageProps = {
  searchParams: Promise<Record<string, string | string[] | undefined>>;
};

export default async function TasksPage({ searchParams }: TasksPageProps) {
  const params = await searchParams;
  const filters = taskFilterSchema.parse({
    status: params.status,
    page: params.page,
  });

  const session = await requireAuth();
  const { tasks, totalPages } = await getTasksForUser(session.user.id, filters);

  return <TaskBoard tasks={tasks} totalPages={totalPages} filters={filters} />;
}
```

```typescript
// features/tasks/schemas/task.schema.ts
export const taskFilterSchema = z.object({
  status: z.enum(["todo", "in_progress", "done", "all"]).default("all"),
  page: z.coerce.number().int().min(1).default(1),
});
```

---

## 6. Optimistic UI (when ticket allows)

Use only when a ticket explicitly requires instant feedback. Default to `revalidatePath` without optimistic updates.

```tsx
"use client";

import { useOptimistic, useTransition } from "react";
import { updateTaskStatus } from "../actions/update-task";
import type { Task, TaskStatus } from "../types/task.types";

export function TaskStatusSelect({ task }: { task: Task }) {
  const [optimisticStatus, setOptimisticStatus] = useOptimistic(task.status);
  const [isPending, startTransition] = useTransition();

  function handleChange(newStatus: TaskStatus) {
    startTransition(async () => {
      setOptimisticStatus(newStatus);
      const result = await updateTaskStatus({
        taskId: task.id,
        status: newStatus,
      });
      if (!result.success) {
        setOptimisticStatus(task.status); // revert
      }
    });
  }

  return (
    <select
      value={optimisticStatus}
      onChange={(e) => handleChange(e.target.value as TaskStatus)}
      disabled={isPending}
    >
      <option value="todo">To Do</option>
      <option value="in_progress">In Progress</option>
      <option value="done">Done</option>
    </select>
  );
}
```

---

## 7. Feature Module Skeleton

When creating a new feature, start with this structure:

```
src/features/notifications/
├── actions/
│   └── mark-as-read.ts
├── components/
│   ├── notification-bell.tsx      # "use client" — interactive
│   └── notification-list.tsx      # Server Component
├── schemas/
│   └── notification.schema.ts
├── types/
│   └── notification.types.ts
├── lib/
│   └── queries.ts
└── index.ts
```

```typescript
// features/notifications/index.ts
export { NotificationBell } from "./components/notification-bell";
export { NotificationList } from "./components/notification-list";
export type { Notification } from "./types/notification.types";
```

---

## 8. Environment Validation

```typescript
// src/lib/env.ts
import { z } from "zod";

const envSchema = z.object({
  DATABASE_URL: z.string().url(),
  NODE_ENV: z.enum(["development", "test", "production"]),
  GITHUB_CLIENT_ID: z.string().min(1),
  GITHUB_CLIENT_SECRET: z.string().min(1),
});

export const env = envSchema.parse(process.env);
```

---

## Anti-Patterns

Do not use these patterns. If you encounter them in existing code, log in KNOWN_ISSUES.md and fix only when in ticket scope.

| Anti-Pattern | Why It's Wrong | Correct Pattern |
|--------------|----------------|-----------------|
| `useEffect` + `fetch` for page data | Defeats SSR, causes loading flashes | Server Component data fetch |
| Business logic in `app/page.tsx` | Violates layer separation | `features/<n>/lib/` |
| `any` type on action input | No compile-time safety | Zod schema + `z.infer` |
| Importing sibling feature | Creates coupling | Compose in `app/` layer |
| `"use client"` on page component | Ships entire page to client | Push client boundary to leaves |
| Mock data in production code | Masks real integration issues | Real implementation or test-only mocks |
| `JSON.parse` without Zod | Runtime crashes on bad data | `schema.safeParse(JSON.parse(raw))` |
| Throwing errors for validation failures | Crashes error boundary | Return `{ success: false, error }` |
| Duplicating Zod schema as interface | Drift between validation and types | `z.infer<typeof schema>` |
| Global client state (Redux, Zustand) | Unnecessary complexity | Server state + URL params |
