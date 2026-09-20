> **Type: REUSABLE** | Copy as-is across projects. Edit only to improve the shared template.

# Skills Map

The control panel for your skill-powered workflow. Maps every installed skill to the phase and moment where it should be invoked. When you or an AI agent asks "which skill do I use right now?" — this file answers.

For the full workflow context, see [WORKFLOW.md](../workflow/WORKFLOW.md).

---

## Phase 1 — Discovery

> Goal: Clarify exactly what to build before writing a line of code or choosing a stack.

| Skill | Invoke when |
|-------|-------------|
| `interview-me` | The very first step. Extract the user's actual intent before anything else. Run until ~95% confidence. |
| `idea-refine` | Intent is clear but the concept needs stress-testing or variations explored. |
| `spec-driven-development` | Intent is confirmed. Write the formal spec and acceptance criteria. |
| `planning-and-task-breakdown` | Spec exists. Break it into ordered foundation items and feature work. |
| `doubt-driven-development` | Plan is drafted. Run an adversarial review before signing off on Phase 1. |

**Gate output:** `ai/project/product/DISCOVERY.md` signed off.

---

## Phase 2 — Foundation

> Goal: Set up only what the project actually needs — no speculative infrastructure.

| Skill | Invoke when |
|-------|-------------|
| `source-driven-development` | Choosing any library or framework. Ground every choice in official docs. |
| `api-and-interface-design` | Defining data models, API contracts, or service boundaries before coding them. |
| `security-and-hardening` | Setting up auth, env validation, input handling, or any security-sensitive layer. |
| `planning-and-task-breakdown` | Foundation areas are identified. Turn them into scoped tickets. |

**Gate output:** `ai/project/FOUNDATION_CHECKLIST.md` — every `required` item marked `done`.

---

## Phase 3 — Feature Development

> Goal: Build features incrementally with production-quality code, ticket by ticket.

| Skill | Invoke when |
|-------|-------------|
| `incremental-implementation` | Every ticket. Always deliver in small, verifiable steps. |
| `test-driven-development` | Every ticket. Write or verify tests before or alongside implementation. |
| `frontend-ui-engineering` | Any ticket that touches user-facing UI, components, or layouts. |
| `api-and-interface-design` | Any ticket that introduces a new endpoint, data contract, or module boundary. |
| `source-driven-development` | Any ticket that requires using a library or framework feature — check the docs first. |
| `security-and-hardening` | Any ticket involving auth, user input, data exposure, or secrets. |
| `code-review-and-quality` | Before every merge. Run a multi-axis quality review. |
| `code-simplification` | When a feature or module has accumulated unnecessary complexity. |
| `performance-optimization` | When profiling reveals a bottleneck — not speculatively. |
| `debugging-and-error-recovery` | When a ticket uncovers unexpected behavior or a bug that needs root-cause diagnosis. |
| `documentation-and-adrs` | When a significant architectural decision is made during a ticket. |
| `git-workflow-and-versioning` | Every commit, branch, PR, and release. |

---

## Cross-Phase — Use Anytime

> These skills are not phase-specific. Invoke them whenever the situation calls for it.

| Skill | Invoke when |
|-------|-------------|
| `context-engineering` | Starting a new session or switching tasks. Set up agent context before loading any ticket. |
| `using-agent-skills` | Unsure which skill applies to the current situation. This meta-skill guides discovery. |
| `observability-and-instrumentation` | Before shipping to production. Add logging, metrics, and tracing. |
| `ci-cd-and-automation` | Setting up or modifying build, test, or deployment pipelines. |
| `shipping-and-launch` | Preparing for any production deployment. Pre-launch checklist and rollback strategy. |
| `browser-testing-with-devtools` | Debugging or verifying UI behavior in a real browser runtime. |
| `extract-design-kit` | Capturing a visual language or design system from a reference site or repository. |
| `graphify` | Exploring codebase structure, architecture, or relationship between files. |
| `deprecation-and-migration` | Removing old systems, APIs, or features safely. |
| `interview-me` | Any time requirements become ambiguous mid-project — not just at Phase 1. |
| `doubt-driven-development` | Before any high-stakes decision (irreversible changes, security-sensitive logic, production). |

---

## Skill → Phase Quick Reference

| Skill | Primary Phase |
|-------|--------------|
| `interview-me` | Phase 1 (also cross-phase) |
| `idea-refine` | Phase 1 |
| `spec-driven-development` | Phase 1 |
| `planning-and-task-breakdown` | Phase 1 + Phase 2 |
| `doubt-driven-development` | Phase 1 (also cross-phase) |
| `source-driven-development` | Phase 2 + Phase 3 |
| `api-and-interface-design` | Phase 2 + Phase 3 |
| `security-and-hardening` | Phase 2 + Phase 3 |
| `incremental-implementation` | Phase 3 |
| `test-driven-development` | Phase 3 |
| `frontend-ui-engineering` | Phase 3 |
| `code-review-and-quality` | Phase 3 |
| `code-simplification` | Phase 3 |
| `performance-optimization` | Phase 3 |
| `debugging-and-error-recovery` | Phase 3 |
| `documentation-and-adrs` | Phase 3 |
| `git-workflow-and-versioning` | Phase 3 |
| `context-engineering` | Cross-phase |
| `using-agent-skills` | Cross-phase |
| `observability-and-instrumentation` | Cross-phase |
| `ci-cd-and-automation` | Cross-phase |
| `shipping-and-launch` | Cross-phase |
| `browser-testing-with-devtools` | Cross-phase |
| `extract-design-kit` | Cross-phase |
| `graphify` | Cross-phase |
| `deprecation-and-migration` | Cross-phase |
