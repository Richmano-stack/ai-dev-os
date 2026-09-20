> **Type: PROJECT-SPECIFIC** | Fill this out during Phase 2. It is the gate document for entering Phase 3.

# Foundation Checklist

This document is the **Phase 2 gate**. The AI agent reads this before touching any feature ticket. If any `required` item is not `done`, it must stop and report what is missing.

**Skills used during Phase 2:** `source-driven-development`, `api-and-interface-design`, `security-and-hardening`

**Phase 2 complete:** `<!-- FILL: yes / not yet -->`

---

## How to Use

For each foundation area below:
1. Set status to `required` or `not needed` based on `ai/project/product/DISCOVERY.md`.
2. If `not needed`, record the reason in `ai/project/memory/DECISIONS_LOG.md`.
3. If `required`, create a ticket in `ai/project/features/_foundation/tickets/` and implement it.
4. When the ticket is merged, mark the item `done`.

**Do not start Phase 3 until every `required` item is `done`.**

---

## Foundation Areas

### Auth

**Status:** `<!-- FILL: required / not needed / done -->`
**Decision log entry:** `<!-- FILL: link or date -->`

- [ ] Auth provider chosen and configured
- [ ] Protected route strategy defined
- [ ] Session management implemented
- [ ] Unauthenticated redirect behavior defined

---

### Database

**Status:** `<!-- FILL: required / not needed / done -->`
**Decision log entry:** `<!-- FILL: link or date -->`

- [ ] Database provider chosen
- [ ] ORM or query layer chosen and configured
- [ ] Initial schema defined and migration run
- [ ] Connection pooling configured (if applicable)
- [ ] Direct vs. pooled connection strategy set

---

### Environment Variable Validation

**Status:** `<!-- FILL: required / not needed / done -->`
**Decision log entry:** `<!-- FILL: link or date -->`

- [ ] All env variables listed in `ai/project/memory/PROJECT_CONTEXT.md`
- [ ] Runtime validation in place (build fails if a required var is missing or malformed)
- [ ] `.env.example` created

---

### Global Error Handling

**Status:** `<!-- FILL: required / not needed / done -->`
**Decision log entry:** `<!-- FILL: link or date -->`

- [ ] Error response format defined (what users see vs. what gets logged)
- [ ] Server-side error logging strategy in place
- [ ] Client-side error boundary or toast strategy defined
- [ ] No raw error messages exposed to users

---

### UI Primitives

**Status:** `<!-- FILL: required / not needed / done -->`
**Decision log entry:** `<!-- FILL: link or date -->`

- [ ] Component library chosen (or custom primitives defined)
- [ ] Base layout and typography set up
- [ ] Design tokens or theme variables defined
- [ ] Core primitives available: Button, Input, Modal/Dialog, Toast/Notification

---

### File Storage

**Status:** `<!-- FILL: required / not needed / done -->`
**Decision log entry:** `<!-- FILL: link or date -->`

- [ ] Storage provider chosen (S3, R2, UploadThing, etc.)
- [ ] Upload strategy defined (direct, server-side, signed URL)
- [ ] File size and type limits defined
- [ ] CDN or serving strategy defined

---

### Background Jobs / Queues

**Status:** `<!-- FILL: required / not needed / done -->`
**Decision log entry:** `<!-- FILL: link or date -->`

- [ ] Queue provider chosen (Trigger.dev, BullMQ, Inngest, etc.)
- [ ] Job categories identified (email, PDF, AI calls, etc.)
- [ ] Retry and failure strategy defined
- [ ] Long-running task timeout limits understood for the hosting platform

---

## Custom Foundation Items

<!-- FILL: Add any project-specific foundation items that don't fit above. -->

### <!-- FILL: Item name -->

**Status:** `<!-- FILL: required / not needed / done -->`

- [ ] <!-- FILL -->

---

## Phase 3 Gate Check

Before marking Phase 2 complete:

- [ ] Every `required` item above is `done`
- [ ] Every `not needed` item has a DECISIONS_LOG entry explaining why
- [ ] `ai/reusable/stack/TECH_STACK.md` is filled in for this project
- [ ] `ai/reusable/stack/ARCHITECTURE.md` is filled in for this project
- [ ] `ai/reusable/stack/TECH_RULES.md` is filled in for this project
- [ ] `ai/project/memory/PROJECT_CONTEXT.md` reflects the current repo structure
- [ ] `Phase 2 complete` field above is set to `yes`
