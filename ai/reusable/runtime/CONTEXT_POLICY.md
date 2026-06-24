> **Type: REUSABLE** | Copy as-is across Next.js projects. Edit only to improve the shared template.

# Context Policy

Rules for what context AI agents should load, in what order, and when to update memory files.

---

## Loading Order

Load context in this sequence. Do not skip steps.

```
1. Active ticket
2. ai/project/memory/PROJECT_CONTEXT.md
3. ai/project/memory/KNOWN_ISSUES.md
4. ai/project/memory/DECISIONS_LOG.md (if architectural area)
5. ai/reusable/stack/TECH_RULES.md
6. ai/reusable/stack/PATTERNS.md
7. ai/reusable/stack/ARCHITECTURE.md (if new feature or unfamiliar area)
8. Existing source code (targeted reads only)
```

---

## What to Load Per Ticket Type

### Feature (`type: feature`)

| Document | Required |
|----------|----------|
| Ticket | Yes |
| PROJECT_CONTEXT.md | Yes |
| KNOWN_ISSUES.md | Yes |
| TECH_RULES.md | Yes |
| PATTERNS.md | Yes |
| ARCHITECTURE.md | Yes |
| DECISIONS_LOG.md | If new patterns or dependencies |
| SCOPE.md | If unsure about phase boundaries |
| USER_STORIES.md | If tracing AC to story |

### Bugfix (`type: bugfix`)

| Document | Required |
|----------|----------|
| Ticket | Yes |
| KNOWN_ISSUES.md | Yes — check if already logged |
| PROJECT_CONTEXT.md | Yes |
| TECH_RULES.md | Yes |
| PATTERNS.md | If fix involves actions/forms |
| Affected source files | Yes — read before changing |

### Refactor (`type: refactor`)

| Document | Required |
|----------|----------|
| Ticket | Yes |
| ARCHITECTURE.md | Yes |
| DECISIONS_LOG.md | Yes — check for constraints |
| PROJECT_CONTEXT.md | Yes |
| All files in ticket scope | Yes — read before changing |

### Chore (`type: chore`)

| Document | Required |
|----------|----------|
| Ticket | Yes |
| DEPENDENCIES.md | If adding/updating packages |
| PROJECT_CONTEXT.md | If changing scripts or config |
| DECISIONS_LOG.md | If architectural tooling choice |

---

## Source Code Reading Strategy

### Targeted Reads (preferred)

Read only files relevant to the ticket:

1. Files listed in `ticket.files.modify` — read fully
2. Files imported by those files — read if needed for context
3. Corresponding schemas, types, and tests — read if creating related code
4. Pattern examples from `PATTERNS.md` — reference, don't re-read entire stack

### Do Not

- Scan the entire repository
- Read every file in a feature module when only one file changes
- Load `node_modules` or generated files
- Read product docs for implementation details (use tickets)

### When to Expand Reads

- Import path doesn't resolve — read the exporting module
- Type error references a file not in scope — read to understand, don't modify
- Pattern is unclear — read similar existing feature for convention reference

---

## Token Budget Guidance

| Priority | Content | Budget Share |
|----------|---------|-------------|
| 1 (highest) | Active ticket | Always loaded |
| 2 | Memory files | Load relevant sections |
| 3 | Stack rules and patterns | Reference as needed |
| 4 | Source code | Targeted files only |
| 5 (lowest) | Product docs | Only for scope questions |

**Rules:**

- Prefer reading a specific file over searching the entire codebase.
- If a file is over 300 lines, read the relevant section, not the whole file.
- Cache mental model of project structure from PROJECT_CONTEXT.md — don't re-read it for every file edit.
- Don't load test files unless writing or fixing tests.

---

## When to Update Memory Files

### PROJECT_CONTEXT.md

Update when the ticket:

- Adds a new top-level directory or feature module
- Adds or removes environment variables
- Adds or removes external integrations
- Changes key commands (scripts)

### DECISIONS_LOG.md

Update when the ticket:

- Chooses between architectural alternatives
- Adds a new dependency not in DEPENDENCIES.md
- Establishes a new pattern not in PATTERNS.md
- Supersedes a previous decision

### KNOWN_ISSUES.md

Update when the ticket:

- Discovers a bug outside its scope (log as open)
- Fixes a logged issue (move to resolved)
- Identifies technical debt (log in debt section)
- Introduces a workaround that future work must respect

### Do Not Update

- `ai/project/product/PRD.md` — human/stakeholder authored
- `ai/project/product/USER_STORIES.md` — human/stakeholder authored
- `ai/project/product/SCOPE.md` — human/stakeholder authored
- `ai/reusable/stack/*` — reusable template files (update only when improving the template itself)
- `ai/reusable/workflow/*` — reusable template files
- `ai/reusable/runtime/*` — reusable template files

---

## Context Freshness

| File | Staleness Risk | Mitigation |
|------|---------------|------------|
| PROJECT_CONTEXT.md | High — repo changes often | Read at start of every ticket |
| KNOWN_ISSUES.md | Medium — issues open/close | Read at start of every ticket |
| DECISIONS_LOG.md | Low — decisions are stable | Read when touching architecture |
| Stack docs | Very low — template files | Read once, reference as needed |
| Ticket | N/A — single source of truth | Always read current version |

If PROJECT_CONTEXT.md appears stale (references files that don't exist), report it but proceed using actual repo structure. Suggest updating the context file.

---

## Multi-Agent Context Isolation

Each agent loads context independently:

- Agents do not share loaded context or conversation history.
- The ticket is the only shared contract between agents.
- Memory files are the shared state — agents read and write them.
- If an agent updates a memory file, the next agent sees the update on its next read.
- Do not assume another agent has already read or updated a file.
