---
description: Plan a task (from any task tracker or freeform) as concise, independently reviewable milestones
agent: build
---

# Plan Task: $ARGUMENTS

## Step 1: Load Applicable Guidance

Read applicable project guidance before planning:

- Read the repository-root `AGENTS.md` if present, then any nearer `AGENTS.md` files governing areas the task will touch.
- Read the repository-root `LEARNINGS.md` if present, plus relevant local notes when they exist.
- Identify applicable OpenCode skills.

`AGENTS.md` is the source for stable project conventions: architecture, naming, error handling, testing, validation, generated files, migrations, configuration, security, and review workflow. Do not restate those conventions in a task plan.

`LEARNINGS.md` contains durable, non-obvious project constraints. Update it only when a fact discovered during planning is specific to the project, likely to recur, stable, and not already documented in `AGENTS.md` or nearby code. Keep additions to 1-3 dated bullets with a source. Never add temporary status, generic advice, or unresolved speculation.

Do not create a replacement guidance file during this command. If important stable conventions are missing from guidance, mention the gap in the final report instead of creating or duplicating guidance.

## Step 2: Fetch Task

`$ARGUMENTS` is a task identifier, issue ID, ticket key, or freeform description.

First, look for `tasks/task-[TASK_ID].definition.md`, matching by identifier or title slug. If found, read it completely.

- If its status is `READY_FOR_TECHNICAL_PLANNING`, treat it as the primary requirements source. Preserve its scope, acceptance criteria, constraints, and decisions. Use the tracker only to refresh metadata or resolve a material change in task content.
- If its status is `DRAFT`, report its open questions and tell the user: `Complete the business definition with /define-task $ARGUMENTS before creating a technical plan.` Then stop.
- If no definition exists and a relevant tracker MCP tool is available, load the task and capture its identifier, title, description, URL, suggested branch, project/space, and status.
- Otherwise, treat `$ARGUMENTS` as freeform task content. If it is too brief to plan, ask the user for the full description.
- If a tracker returns not found, tell the user: `Could not find task '$ARGUMENTS' in the available tracker. Verify the ID, or paste the task description directly.` Then stop.

## Step 3: Analyze Only What Affects This Task

Inspect one or two analogous implementations, tests, and configuration surfaces only where they are needed to establish a task decision, boundary, risk, or validation approach. Follow applicable `AGENTS.md` guidance first.

Record only task-specific constraints and sources, for example:

```markdown
## Applicable Constraints
- `src/payments/AGENTS.md`: provider calls use the shared retry wrapper.
- `src/payments/refunds.ts`: analogous idempotency behavior.
```

Do not produce a generic convention inventory, inspect unrelated layers, or copy routine patterns into the plan.

### Requirements and Questions

Classify uncertainty:

- **Blocking question:** ask before planning when it affects external behavior, data shape, security, compatibility, cost, or safe validation.
- **Assumption:** use a low-risk, convention-backed default; record it in the plan.
- **Implementation detail:** leave it to the implementer under applicable guidance and nearby code.

Use the `question` tool only for blocking questions. State the decision, available options, recommended option, and consequence. Record the answer and resulting decision in the plan.

Cross-check high-risk requirements when relevant: identity, uniqueness, relationships, data types, search, performance, configuration, dependencies, and security. Record only conflicts, decisions, and constraints; do not create a table for requirements that are already aligned.

## Step 4: Define Reviewable Milestones

Create the smallest coherent sequence of vertical slices. A milestone must:

- Deliver a meaningful observable capability or a safe prerequisite.
- Have explicit acceptance criteria and focused local verification.
- Be understandable and reviewable as one purpose.
- Leave the system usable, except for explicitly approved scaffolding.
- Be independently reviewed and approved before dependent later work starts.

Do not split merely by technical layer such as models, services, and tests. Prefer outcome-oriented milestones. For example, use `Persist the setting with migration coverage`, not `Create model classes`.

Split a task when a reviewer cannot explain the change in one sentence, it changes independently deployable behavior, it combines schema/API/UI/integration or rollout concerns, a later step depends on an unreviewed contract, or focused validation cannot isolate a failure. Keep tightly coupled work together when splitting would leave unusable partial behavior. Small tasks may have one milestone.

Describe outcomes, scope, acceptance criteria, essential architecture decisions, and external contracts. Do not prescribe incidental method names, signatures, constructors, or implementation mechanics unless externally required.

For each milestone, identify expected files or areas, applicable skills, dependencies, local validation command(s), and the human-review boundary. File lists are estimates, not a binding implementation contract.

## Step 5: Create Context File

Ensure `tasks/` exists at the repository root. Create `tasks/task-[TASK_ID].context.md`; when no ID exists, derive a short kebab-case title slug.

This is a human-readable plan and lightweight execution record. Keep it concise. Subagents receive focused extracts, not the entire file.

```markdown
# [TASK_ID]: [Task Title]

## Task
- Source: [URL or `freeform`]
- Business definition: [path, if used]
- Branch: [suggested branch, if available]
- Status: PLANNED
- Created: [timestamp]
- Updated: [timestamp]

## Goal
[One or two sentences describing the user-visible outcome.]

## Scope
- In: [...]
- Out: [...]

## Applicable Constraints
- [Only task-specific constraints, each with a source path or task reference.]

## Decisions
| Decision | Rationale | Source |
|---|---|---|
| [decision] | [why] | [user / task / code reference] |

## Assumptions And Open Questions
- Assumption: [low-risk default and source]
- Blocking question: [only if planning cannot proceed]

## Milestones

### M1: [Outcome-oriented title]
- Status: PLANNED
- Outcome: [observable capability or safe prerequisite]
- Scope: [expected files or areas]
- Dependencies: [none or milestone/task dependency]
- Acceptance criteria:
  - [...]
- Local verification: `[focused command]` or `[how to verify when no command exists]`
- AI review: required after local verification
- Human review: required before M2; explicit user consent is sufficient

### M2: [Outcome-oriented title]
- Status: PLANNED
- Outcome: [...]
- Scope: [...]
- Dependencies: M1 approved
- Acceptance criteria:
  - [...]
- Local verification: `[focused command]`
- AI review: required after local verification
- Human review: required before the next milestone or final completion; explicit user consent is sufficient

## Execution Record
- [timestamp] Plan created.
- [timestamp] [milestone status, validation, AI review result, or human approval]

## User Notes
[Optional user-supplied notes.]
```

Include the tracker URL and a compact task summary. When a business definition was used, link to it rather than duplicating its full content. Do not copy the full tracker description unless it is needed to preserve acceptance criteria that cannot be concisely summarized.

For configuration changes, include every established configuration surface in the relevant milestone scope.

## Step 6: Report

Tell the user:

1. Context file path.
2. Goal and milestone summary, including the local verification and approval boundary for each milestone.
3. Blocking questions resolved and assumptions made.
4. Relevant task-specific constraints and decisions.
5. `LEARNINGS.md` update status, plus any guidance gaps noticed.
6. `Run /start-task $ARGUMENTS to implement M1.`
