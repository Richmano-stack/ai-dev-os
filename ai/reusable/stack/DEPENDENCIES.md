> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Dependencies

Approved dependencies, addition process, and forbidden packages for this stack.

---

## Core Dependencies (always present)

| Package | Purpose | Version Policy |
|---------|---------|----------------|
| `next` | Framework | Latest stable |
| `react`, `react-dom` | UI library | Match Next.js peer dep |
| `typescript` | Type safety | Latest stable |
| `zod` | Runtime validation | Latest stable |
| `react-hook-form` | Form state management | Latest stable |
| `@hookform/resolvers` | Zod + RHF integration | Latest stable |
| `tailwindcss` | Styling | Latest stable |
| `clsx` + `tailwind-merge` | Class name utility (`cn()`) | Latest stable |

---

## Approved Optional Dependencies

Add these when a ticket requires them. Log the choice in DECISIONS_LOG if choosing between alternatives.

### Database

| Package | Purpose | Pick One |
|---------|---------|----------|
| `prisma` + `@prisma/client` | ORM with migrations | Yes |
| `drizzle-orm` + `drizzle-kit` | Lightweight ORM | Yes |

### Authentication

| Package | Purpose |
|---------|---------|
| `next-auth` (Auth.js v5) | OAuth, session management |

### UI

| Package | Purpose |
|---------|---------|
| `@radix-ui/*` | Accessible primitives (via shadcn/ui) |
| `lucide-react` | Icons |
| `class-variance-authority` | Component variants (shadcn) |

### URL State

| Package | Purpose |
|---------|---------|
| `nuqs` | Type-safe URL search params |

### Testing

| Package | Purpose |
|---------|---------|
| `vitest` | Unit and integration tests |
| `@testing-library/react` | Component tests |
| `@testing-library/user-event` | User interaction simulation |
| `@playwright/test` | E2E tests |

### Development

| Package | Purpose |
|---------|---------|
| `eslint` + `eslint-config-next` | Linting |
| `prettier` | Formatting |
| `@types/*` | Type definitions |

---

## Add-New-Dependency Checklist

Before adding any package not listed above:

1. **Is it necessary?** Can the requirement be met with existing dependencies or standard library?
2. **Bundle impact.** Run `npx bundlephobia <package>` — reject if > 50KB minified without justification.
3. **Maintenance.** Check last publish date, open issues, weekly downloads on npm.
4. **Security.** Check for known vulnerabilities (`npm audit`).
5. **License.** Must be MIT, Apache-2.0, or BSD compatible.
6. **TypeScript support.** Must have built-in types or reliable `@types/` package.
7. **Log the decision.** Add entry to `ai/project/memory/DECISIONS_LOG.md` with rationale.
8. **Update this file.** Add to the approved list.

---

## Forbidden Packages and Patterns

| Package / Pattern | Reason | Alternative |
|-------------------|--------|-------------|
| `moment` / `moment-timezone` | Large bundle, legacy API | `date-fns` or native `Intl.DateTimeFormat` |
| `lodash` (full) | Large bundle | Native methods or `lodash-es` with specific imports |
| `axios` | Unnecessary — use native `fetch` | `fetch` (built into Next.js) |
| `redux` / `@reduxjs/toolkit` | Unnecessary global state | Server Components + URL state |
| `styled-components` / `emotion` | Conflicts with Tailwind stack | Tailwind CSS |
| `express` | Not needed in Next.js | API Route Handlers or Server Actions |
| `jsonwebtoken` (client-side) | Security risk | Server-side session via Auth.js |
| `dotenv` in application code | Use validated env.ts | `src/lib/env.ts` with Zod |
| `any` type packages | Defeats TypeScript | Proper typing |
| `request` / `node-fetch` v2 | Deprecated | Native `fetch` |

---

## Version Pinning

- **Production dependencies:** pin to exact version or use lockfile (`package-lock.json` / `pnpm-lock.yaml`).
- **Do not use `*` or `latest`** in `package.json` version ranges.
- **Renovate/Dependabot:** enable for security patches. Review major version bumps manually.

---

## `cn()` Utility (required setup)

```typescript
// src/lib/utils.ts
import { clsx, type ClassValue } from "clsx";
import { twMerge } from "tailwind-merge";

export function cn(...inputs: ClassValue[]) {
  return twMerge(clsx(inputs));
}
```

---

## Scripts (standard package.json)

```json
{
  "scripts": {
    "dev": "next dev",
    "build": "next build",
    "start": "next start",
    "lint": "next lint",
    "typecheck": "tsc --noEmit",
    "test": "vitest",
    "test:e2e": "playwright test"
  }
}
```

Every PR must pass `lint`, `typecheck`, and `test` before merge.

---

## Import Conventions

```typescript
// External packages first
import { z } from "zod";
import { useForm } from "react-hook-form";

// Internal absolute imports (@/ alias)
import { db } from "@/lib/db";
import { Button } from "@/components/ui/button";

// Relative imports within the same feature only
import { createTaskSchema } from "../schemas/task.schema";
import { getTasksForUser } from "../lib/queries";
```

Never import from another feature's internal files. Use the feature's `index.ts` public API or compose in `app/`.
