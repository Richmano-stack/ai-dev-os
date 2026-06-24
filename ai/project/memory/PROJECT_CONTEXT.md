> **Type: PROJECT-SPECIFIC** | Customize for your product. Update as requirements and context evolve.

# Project Context

---

## Purpose

PROJECT_CONTEXT.md is the **operational map** of this specific codebase. It gives AI agents and developers the context they need to work effectively without re-discovering the repo on every task.

**Update this file when:**

- Repository structure changes significantly (new top-level directories, feature modules)
- New external integrations are added or removed
- Environment variables are added, renamed, or removed
- Key conventions or glossary terms are established
- Deployment or infrastructure setup changes

**Do not update for:** routine feature work, bug fixes, or decisions (use DECISIONS_LOG.md instead).

---

## Instructions

1. **Describe the repo structure** with a tree and one-line purpose per directory.
2. **List environment variables by name only** — never include secret values.
3. **Document integrations** with purpose, not implementation details.
4. **Maintain a glossary** of domain terms used in this project.
5. **Keep it factual and current.** Remove stale entries when things change.
6. **Link to external docs** (API docs, design system) rather than duplicating them.
7. **Do not invent details.** If unknown, leave the template marker and fill in when known.

---

## Template

```markdown
# Project Context

**Last updated:** <!-- FILL: YYYY-MM-DD -->
**Repository:** <!-- FILL: org/repo or local path -->
**Primary branch:** <!-- FILL: main | master -->

---

## Overview

<!-- FILL: 2-3 sentences describing what this application does. -->

## Repository Structure

\`\`\`
<!-- FILL: Directory tree with brief annotations -->
src/
├── app/           # Next.js App Router pages and layouts
├── features/      # Feature modules (domain logic)
├── components/    # Shared UI components
└── lib/           # Shared utilities and infrastructure
\`\`\`

| Path | Purpose |
|------|---------|
| <!-- FILL --> | <!-- FILL --> |

## Tech Stack

| Layer | Technology | Notes |
|-------|------------|-------|
| Framework | <!-- FILL --> | <!-- FILL --> |
| Database | <!-- FILL --> | <!-- FILL --> |
| Auth | <!-- FILL --> | <!-- FILL --> |
| Hosting | <!-- FILL --> | <!-- FILL --> |

## Environment Variables

> List names only. Never commit values. Document in `.env.example`.

| Variable | Required | Description |
|----------|----------|-------------|
| <!-- FILL --> | yes/no | <!-- FILL --> |

## External Integrations

| Service | Purpose | Docs |
|---------|---------|------|
| <!-- FILL --> | <!-- FILL --> | <!-- FILL: URL --> |

## Key Commands

\`\`\`bash
# Development
<!-- FILL -->

# Database
<!-- FILL -->

# Testing
<!-- FILL -->

# Linting / type check
<!-- FILL -->
\`\`\`

## Glossary

| Term | Definition |
|------|------------|
| <!-- FILL --> | <!-- FILL --> |

## Related Documents

- PRD: `ai/project/product/PRD.md`
- Scope: `ai/project/product/SCOPE.md`
- Architecture: `ai/reusable/stack/ARCHITECTURE.md`
- Decisions: `ai/project/memory/DECISIONS_LOG.md`
- Known issues: `ai/project/memory/KNOWN_ISSUES.md`
```

---

## Examples

> **Note:** Fictional example for format demonstration only.

```markdown
# Project Context

**Last updated:** 2026-02-01
**Repository:** acme/task-board
**Primary branch:** main

---

## Overview

A team task management web app. Engineers sign in via GitHub OAuth and manage assigned tasks on a kanban-style dashboard.

## Repository Structure

\`\`\`
src/
├── app/
│   ├── (auth)/          # Public auth routes
│   ├── (dashboard)/     # Protected dashboard routes
│   └── api/             # Route handlers (webhooks only)
├── features/
│   ├── auth/            # Authentication logic
│   └── tasks/           # Task CRUD and queries
├── components/
│   └── ui/              # shadcn/ui primitives
└── lib/
    ├── db/              # Prisma client and helpers
    └── env.ts           # Validated environment config
\`\`\`

## Environment Variables

| Variable | Required | Description |
|----------|----------|-------------|
| DATABASE_URL | yes | PostgreSQL connection string |
| GITHUB_CLIENT_ID | yes | OAuth app client ID |
| GITHUB_CLIENT_SECRET | yes | OAuth app secret |
| NEXTAUTH_SECRET | yes | Session encryption key |

## Glossary

| Term | Definition |
|------|------------|
| Task | A unit of work assignable to one team member |
| Board | A collection of tasks grouped by status columns |
```
