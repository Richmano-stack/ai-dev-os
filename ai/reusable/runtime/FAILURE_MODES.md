> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Failure Modes

Catalog of common AI agent failures, their detection signals, and recovery procedures.

---

## 1. Scope Creep

**What:** Agent implements features, refactors, or fixes not listed in the ticket.

**Detection signals:**

- Diff includes files not in `ticket.files`
- New exports or components not in `scope.in_scope`
- Commit message mentions work beyond ticket title
- "While I was here, I also..." in completion report

**Recovery:**

1. Revert changes outside scope.
2. Report what was reverted and why.
3. Suggest creating a new ticket for the extra work.
4. Re-run quality checks on the scoped diff only.

**Prevention:** Read `ticket.files` before every file edit. Run scope check in post-execution self-review.

---

## 2. Hallucinated APIs

**What:** Agent uses functions, imports, types, or packages that don't exist in the project.

**Detection signals:**

- Import resolves to a non-existent module
- Typecheck fails with "Cannot find module" or "Property does not exist"
- Agent references a pattern not in PATTERNS.md or existing code
- Agent invents environment variables not in PROJECT_CONTEXT.md

**Recovery:**

1. Stop implementation.
2. Search the actual codebase for the correct API.
3. If no pattern exists, ask for clarification or propose an approach.
4. Do not create stub/placeholder implementations to satisfy the hallucinated API.

**Prevention:** Read existing code before writing. Use PATTERNS.md as the primary reference. Verify imports exist.

---

## 3. Skipped Validation

**What:** Agent accepts unvalidated input in Server Actions, API routes, or forms.

**Detection signals:**

- Server Action parameter typed as `any` or `unknown` without Zod parse
- No `safeParse` or `parse` call before using input
- Form submits data without `zodResolver`
- Environment variables accessed via `process.env` outside `env.ts`

**Recovery:**

1. Add Zod schema for the input.
2. Add `safeParse` with error return in Server Action.
3. Wire `zodResolver` in form if applicable.
4. Re-run tests.

**Prevention:** Every Server Action starts with schema validation. Copy pattern from PATTERNS.md section 1.

---

## 4. Client/Server Boundary Violation

**What:** Server-only code (database, secrets, server modules) imported in Client Components.

**Detection signals:**

- `"use client"` file imports from `lib/db` or `lib/env`
- `process.env.SECRET_*` in a client component
- Build error: "You're importing a component that needs server-only"
- Bundle includes server libraries

**Recovery:**

1. Move data fetching to Server Component parent.
2. Pass data as props to Client Component.
3. Move mutation to Server Action called from client.
4. Verify build succeeds.

**Prevention:** Server Components by default. Push `"use client"` to leaf interactive components only.

---

## 5. Cross-Feature Import

**What:** Agent imports from one feature module into another, violating architecture boundaries.

**Detection signals:**

- `import { ... } from "@/features/other-feature/..."`
- Import path crosses `features/<a>/` into `features/<b>/`
- Circular dependency warnings

**Recovery:**

1. Remove the cross-feature import.
2. Compose both features in the `app/` route page instead.
3. If logic is truly shared, extract to `lib/` (requires scope update or new ticket).

**Prevention:** Read ARCHITECTURE.md dependency rules. Only import from feature's `index.ts` in `app/` layer.

---

## 6. Mock Implementation in Production

**What:** Agent creates fake data, stub functions, or placeholder logic instead of real implementation.

**Detection signals:**

- Hardcoded arrays returned instead of database queries
- `// TODO: implement` in production code paths
- `return { success: true, data: mockData }`
- `Math.random()` for IDs
- Comments like "replace with real API call"

**Recovery:**

1. Replace mock with real implementation using project patterns.
2. If real implementation is blocked (missing dependency, missing schema), stop and report blocker.
3. Do not merge mock code.

**Prevention:** Ticket must explicitly allow mocks. Default is production-ready code.

---

## 7. Type Drift

**What:** TypeScript types defined separately from Zod schemas, causing validation/type mismatch.

**Detection signals:**

- Manual `interface` or `type` that mirrors a Zod schema
- Type allows values that schema rejects
- `as SomeType` casts after parsing

**Recovery:**

1. Remove manual type definition.
2. Use `z.infer<typeof schema>` everywhere.
3. Re-run typecheck.

**Prevention:** Types always derived from schemas. One schema per data shape.

---

## 8. Auth Bypass

**What:** Server Action or sensitive route lacks authentication/authorization check.

**Detection signals:**

- Server Action with no `requireAuth()` or session check
- Mutation callable by unauthenticated users
- No ownership check before delete/update

**Recovery:**

1. Add auth check at the top of the action.
2. Add ownership/permission check before mutation.
3. Add test case for unauthorized access.

**Prevention:** Copy auth pattern from PATTERNS.md section 4. Every action starts with auth.

---

## 9. Missing Revalidation

**What:** Server Action mutates data but UI shows stale state.

**Detection signals:**

- No `revalidatePath` or `revalidateTag` after write
- User reports data not updating without refresh
- Cache headers preventing fresh data

**Recovery:**

1. Add `revalidatePath` for affected routes.
2. Add `revalidateTag` if using tagged cache.
3. Test that UI updates after mutation.

**Prevention:** Every mutation action ends with revalidation. See PATTERNS.md section 1.

---

## 10. Dependency Conflicts (Parallel Execution)

**What:** Two agents modify the same files or contracts simultaneously.

**Detection signals:**

- Merge conflicts on PR
- Type errors after merging parallel branches
- One agent's changes break another's tests

**Recovery:**

1. Stop both agents.
2. Identify overlapping files/contracts.
3. Merge sequentially per PARALLELISM_RULES.md.
4. Re-run full CI on merged result.

**Prevention:** Run parallelism checklist before starting. Default to sequential when in doubt.

---

## 11. Unnecessary useEffect

**What:** Agent adds `useEffect` for derivable state, initial data, or logic that belongs in an event handler.

**Detection signals:**

- New `useEffect` in diff without external-system justification
- State synced from props inside an effect (`setX(props.y)`)
- `fetch` or Server Action call inside `useEffect` on mount
- Form reset logic in `useEffect` when `key` would suffice

**Recovery:**

1. Refactor to Server Component for initial data.
2. Replace prop-sync effects with derived render.
3. Move user-triggered logic to event handlers + Server Actions.
4. Use `key` on child components to reset state when inputs change.

**Prevention:** Read TECH_RULES.md useEffect section and PATTERNS.md section 9 before adding `"use client"` logic.

---

## Escalation Protocol

When a failure mode is detected:

```
1. STOP current work
2. IDENTIFY which failure mode (use IDs above)
3. REPORT:
   - Failure mode: [number and name]
   - Detection: [what signal triggered this]
   - Impact: [what is affected]
   - Recovery: [steps taken or needed]
4. DO NOT continue until recovery is complete or escalation is acknowledged
```

### When to Escalate to Human

- Recovery requires changes outside ticket scope
- Failure indicates a gap in stack docs or patterns
- Same failure mode occurs twice on the same ticket
- Production data or security is at risk
- Agent is unsure which failure mode applies

---

## Failure Mode Quick Reference

| ID | Name | Severity | Stop Work? |
|----|------|----------|------------|
| 1 | Scope creep | Medium | Yes — revert |
| 2 | Hallucinated APIs | High | Yes — verify |
| 3 | Skipped validation | Critical | Yes — fix |
| 4 | Client/server violation | Critical | Yes — fix |
| 5 | Cross-feature import | Medium | Yes — restructure |
| 6 | Mock implementation | High | Yes — implement |
| 7 | Type drift | Medium | Yes — fix |
| 8 | Auth bypass | Critical | Yes — fix |
| 9 | Missing revalidation | Medium | Yes — fix |
| 10 | Dependency conflicts | High | Yes — sequence |
| 11 | Unnecessary useEffect | Medium | Yes — refactor |
