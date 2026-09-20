> **Type: REUSABLE** | Copy as-is across projects. Edit only to improve the shared template.

# AI Development OS

A skill-powered, three-phase workspace for AI-assisted, feature-based development. Stack-agnostic. Copy into any project.

---

## How It Works

Every project follows three sequential phases. The AI agent cannot proceed to the next phase until the current one produces a confirmed output.

```
Phase 1 — Discovery      → output: ai/project/product/DISCOVERY.md (signed off)
               ↓ gate
Phase 2 — Foundation     → output: ai/project/FOUNDATION_CHECKLIST.md (all required items done)
               ↓ gate
Phase 3 — Feature Dev    → tickets in ai/project/features/<feature>/tickets/
               ↓ loop
         Lessons → project memory or promoted back to reusable template
```

For the full workflow, see [ai/reusable/workflow/WORKFLOW.md](reusable/workflow/WORKFLOW.md).
For which skill to use at each phase, see [ai/reusable/runtime/SKILLS_MAP.md](reusable/runtime/SKILLS_MAP.md).

---

## Quick Start

### 1. Copy into your project

```bash
cp -r ai/ /path/to/your-project/ai/
cp -r .cursor/rules/ /path/to/your-project/.cursor/rules/
```

### 2. Run Phase 1 — Discovery

Open an AI agent session and invoke the `interview-me` skill. Do not write code or choose a stack yet.

When the session produces a clear, confirmed intent, fill in `ai/project/product/DISCOVERY.md` and mark it signed off.

### 3. Run Phase 2 — Foundation

Based on `DISCOVERY.md`, fill in `ai/project/FOUNDATION_CHECKLIST.md`. Mark each area as `required` or `not needed`. Create foundation tickets in `ai/project/features/_foundation/tickets/` and implement them. When every `required` item is `done`, Phase 2 is complete.

Also fill in the stack files:
- `ai/reusable/stack/TECH_STACK.md`
- `ai/reusable/stack/ARCHITECTURE.md`
- `ai/reusable/stack/TECH_RULES.md`
- `ai/reusable/stack/PATTERNS.md`

### 4. Run Phase 3 — Feature Development

Create a feature folder and start writing tickets:

```
ai/project/features/<feature_name>/tickets/TICKET-001-short-slug.md
```

Use `ai/reusable/runtime/TICKET_EXECUTION_PROMPT.md` to kick off each ticket with an AI agent.

---

## Folder Map

```
ai/
├── LEGEND.md                     REUSABLE — type reference
├── README.md                     REUSABLE — this file
│
├── project/                      PROJECT-SPECIFIC
│   ├── FOUNDATION_CHECKLIST.md     Phase 2 gate document
│   ├── product/
│   │   ├── DISCOVERY.md            Phase 1 gate document (fill during interview)
│   │   ├── PRD.md                  Product requirements
│   │   ├── USER_STORIES.md         User stories and acceptance criteria
│   │   └── SCOPE.md                Phase boundaries
│   ├── memory/
│   │   ├── PROJECT_CONTEXT.md      Repo map and operational context
│   │   ├── DECISIONS_LOG.md        Architecture decision records
│   │   └── KNOWN_ISSUES.md         Bugs, debt, and workarounds
│   └── features/                   Feature-based organization
│       └── <feature_name>/
│           └── tickets/            One Markdown file per ticket
│
└── reusable/                     REUSABLE
    ├── stack/
    │   ├── TECH_STACK.md           Technology choices (fill per project)
    │   ├── ARCHITECTURE.md         Architecture rules (fill per project)
    │   ├── TECH_RULES.md           Hard technical constraints (fill per project)
    │   ├── PATTERNS.md             Copy-paste patterns (fill per project)
    │   └── DEPENDENCIES.md         Approved packages and addition process
    ├── workflow/
    │   ├── WORKFLOW.md             Three-phase lifecycle (start here)
    │   ├── TICKET_SCHEMA.md        Required ticket fields and example
    │   ├── PARALLELISM_RULES.md    When tickets can run in parallel
    │   ├── EXECUTION_RULES.md      Rules for implementers
    │   └── MERGE_RULES.md          Branch, PR, and merge procedures
    └── runtime/
        ├── SKILLS_MAP.md               Which skill to use at each phase
        ├── SYSTEM_PROMPT.md            Copy-paste agent system prompt
        ├── TICKET_EXECUTION_PROMPT.md  Copy-paste per-ticket chat prompt
        ├── AGENT_RULES.md              Do/don't lists and protocols
        ├── CONTEXT_POLICY.md           What to load and when
        └── FAILURE_MODES.md            Failure catalog and recovery
```

---

## File Types

| Type | Folder | Action |
|------|--------|--------|
| **PROJECT-SPECIFIC** | `ai/project/` | Fill in for your product |
| **REUSABLE** | `ai/reusable/` + `.cursor/rules/` | Copy unchanged to new projects |

See [LEGEND.md](LEGEND.md) for the complete reference.

---

## Onboarding Checklist

- [ ] Copy `ai/` and `.cursor/rules/` into the project
- [ ] Run Phase 1: use `interview-me` skill → fill `ai/project/product/DISCOVERY.md`
- [ ] Run Phase 2: fill `ai/project/FOUNDATION_CHECKLIST.md` → implement foundation tickets
- [ ] Fill in `ai/reusable/stack/` files for your specific stack
- [ ] Fill in `ai/project/product/PRD.md`, `USER_STORIES.md`, `SCOPE.md`
- [ ] Initialize `ai/project/memory/DECISIONS_LOG.md`
- [ ] Initialize `ai/project/memory/KNOWN_ISSUES.md`
- [ ] Create your first feature folder in `ai/project/features/`
- [ ] Verify `.cursor/rules/base.mdc` is active (`alwaysApply: true`)

---

## Key Principles

1. **Phases are sequential.** Discovery → Foundation → Features. No skipping.
2. **Foundation is conditional.** Only set up what the project actually needs.
3. **Every feature starts from a ticket.** No ad-hoc coding.
4. **Skills map to phases.** See `ai/reusable/runtime/SKILLS_MAP.md`.
5. **The template evolves.** Lessons from projects feed back into `ai/reusable/`.

---

## For AI Agents

1. Read `ai/reusable/workflow/WORKFLOW.md` — determine the current phase.
2. Check the phase gate document (`DISCOVERY.md` or `FOUNDATION_CHECKLIST.md`).
3. If gate is not met — STOP and report what is missing.
4. If in Phase 3 — use `ai/reusable/runtime/TICKET_EXECUTION_PROMPT.md` with `@` your ticket file.
5. Check `ai/reusable/runtime/SKILLS_MAP.md` to confirm which skill to invoke.
