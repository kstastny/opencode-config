---
description: Create a human-reviewable implementation plan from approved task definition and research
agent: build
---

# Task Plan: $ARGUMENTS

## Purpose

Turn an approved business definition and technical research document into a concise, reviewable implementation plan. This is design work, not implementation: select the approach, define essential decisions and validation, and create milestones only when they improve safe delivery and reviewability.

## Step 1: Load Approved Inputs And Guidance

Find and read these repository-root files, matching `$ARGUMENTS` by task ID or slug:

- `tasks/task-[TASK_ID].definition.md`
- `tasks/task-[TASK_ID].research.md`

If no definition exists, tell the user: `No business definition found for '$ARGUMENTS'. Run /task-define $ARGUMENTS first.` Then stop.

If its status is `DRAFT`, tell the user: `Complete the business definition with /task-define $ARGUMENTS before planning.` Then stop.

If no research document exists, tell the user: `No technical research found for '$ARGUMENTS'. Run /task-research $ARGUMENTS first.` Then stop.

If its status is not `APPROVED_FOR_PLANNING`, tell the user: `Technical research for '$ARGUMENTS' requires human approval. Run /task-research $ARGUMENTS to review or approve it.` Then stop.

Read applicable project guidance before planning:

- Read the repository-root `AGENTS.md` if present, then any nearer `AGENTS.md` files governing planned areas.
- Read the repository-root `LEARNINGS.md` if present, plus relevant local notes when they exist.
- Identify applicable OpenCode skills.

`AGENTS.md` is the source for stable project conventions: architecture, naming, error handling, testing, validation, generated files, migrations, configuration, security, and review workflow. Do not restate those conventions in the plan.

`LEARNINGS.md` contains durable, non-obvious project constraints. Update it only when a fact discovered during planning is specific to the project, likely to recur, stable, and not already documented in `AGENTS.md` or nearby code. Keep additions to 1-3 dated bullets with a source. Never add temporary status, generic advice, or unresolved speculation.

## Step 2: Resume Or Start Planning

Look for `tasks/task-[TASK_ID].plan.md`.

- If it is `AWAITING_HUMAN_PLAN_APPROVAL` and the user has not explicitly approved it in the current conversation, report its approach, milestones, decisions, validation, and open risks. Ask for approval and stop.
- Explicit conversational approval is sufficient. Do not infer approval merely because `/task-plan` was invoked again.
- If it is `AWAITING_HUMAN_PLAN_APPROVAL` and the user has explicitly approved it, set status to `APPROVED_FOR_IMPLEMENTATION`, update its timestamp and execution record, and report: `Plan approved. Run /task-implement $ARGUMENTS to implement the first eligible milestone.` Then stop.
- If it is already `APPROVED_FOR_IMPLEMENTATION`, report that it is approved and stop.
- If it is `IN_PROGRESS` or `NEEDS_REVISION`, read it completely and continue the missing planning work.

## Step 3: Design The Implementation

Use the definition as the business contract and the research document as the factual basis. Preserve acceptance criteria, scope, constraints, and approved decisions.

Create a plan that states:

- The intended behavior and approach.
- Essential architecture decisions and external contracts.
- Expected files or areas, as estimates rather than a binding implementation contract.
- Required configuration, migration, integration, compatibility, privacy, security, and rollout work when applicable.
- Focused local validation for each unit of work and final validation for the task.

Do not redo broad investigation. Perform only targeted follow-up when a research finding is stale, incomplete, or insufficient to choose a responsible approach. Record the new evidence in the research document before relying on it.

Classify uncertainty:

- **Blocking question:** ask before planning when it affects external behavior, data shape, security, compatibility, cost, rollout, or safe validation.
- **Assumption:** use a low-risk, convention-backed default and record it in the plan.
- **Implementation detail:** leave it to the implementer under applicable guidance and nearby code.

Use the `question` tool only for blocking questions. State the decision, available options, recommended option, and consequence. Record the answer and resulting decision in the plan.

## Step 4: Define Reviewable Milestones

Use the research document's delivery-shape recommendation to decide whether milestones are needed. Small tasks may have one milestone.

A milestone must:

- Deliver a meaningful observable capability or a safe prerequisite.
- Have explicit acceptance criteria and focused local verification.
- Be understandable and reviewable as one purpose.
- Leave the system usable, except for explicitly approved scaffolding.
- Be independently reviewed and approved before dependent later work starts.

Do not split merely by technical layer such as models, services, and tests. Prefer outcome-oriented milestones. Split when a reviewer cannot explain the change in one sentence, it changes independently deployable behavior, it combines schema/API/UI/integration or rollout concerns, a later step depends on an unreviewed contract, or focused validation cannot isolate a failure. Keep tightly coupled work together when splitting would leave unusable partial behavior.

Describe outcomes, scope, acceptance criteria, essential architecture decisions, and external contracts. Do not prescribe incidental method names, signatures, constructors, or implementation mechanics unless externally required.

For each milestone, identify expected files or areas, applicable skills, dependencies, local validation command(s), and the human-review boundary.

## Step 5: Create Or Update The Plan

Ensure `tasks/` exists at the repository root. Create or update `tasks/task-[TASK_ID].plan.md`. Set its status to `AWAITING_HUMAN_PLAN_APPROVAL` only when all blocking questions are resolved. Otherwise set it to `NEEDS_REVISION` and preserve the blockers.

```markdown
# [TASK_ID]: [Task Title] - Implementation Plan

## Status
- Status: IN_PROGRESS / NEEDS_REVISION / AWAITING_HUMAN_PLAN_APPROVAL / APPROVED_FOR_IMPLEMENTATION / COMPLETED
- Business definition: `tasks/task-[TASK_ID].definition.md`
- Technical research: `tasks/task-[TASK_ID].research.md`
- Source: [URL or `freeform`]
- Branch: [suggested branch, if available]
- Created: [timestamp]
- Updated: [timestamp]

## Goal
[One or two sentences describing the user-visible outcome.]

## Scope
- In: [...]
- Out: [...]

## Intended Approach
[Concise design-level description of the behavior and component interaction.]

## Applicable Constraints
- Do not run executables that connect to external services or databases.
- Do not execute migration/import tools. Only build and test.
- Ask for confirmation immediately before any command that can write external data.
- [Only task-specific constraints, each with a source path or task reference.]

## Decisions
| Decision | Rationale | Source |
|---|---|---|
| [decision] | [why] | [user / task / research / code reference] |

## Assumptions And Open Questions
- Assumption: [low-risk default and source]
- Blocking question: [only if planning cannot proceed]

## Milestones

### M1: [Outcome-oriented title]
- Status: PLANNED
- Outcome: [observable capability or safe prerequisite]
- Scope: [expected files or areas]
- Applicable skills: [skills or project convention]
- Dependencies: [none or milestone/task dependency]
- Essential decisions and contracts: [...]
- Acceptance criteria:
  - [...]
- Local verification: `[focused command]` or `[how to verify when no command exists]`
- AI review: required after local verification
- Human review: required before M2; explicit user consent is sufficient

### M2: [Outcome-oriented title]
- Status: PLANNED
- Outcome: [...]
- Scope: [...]
- Applicable skills: [...]
- Dependencies: M1 approved
- Essential decisions and contracts: [...]
- Acceptance criteria:
  - [...]
- Local verification: `[focused command]`
- AI review: required after local verification
- Human review: required before the next milestone or final completion; explicit user consent is sufficient

## Final Validation
- [Required full validation command or procedure and source]

## Execution Record
- [timestamp] Plan created.
- [timestamp] [plan approval, milestone status, validation, AI review result, or human approval]

## User Notes
[Optional user-supplied notes.]
```

Include a compact task summary and links to the definition and research rather than duplicating them. For configuration changes, include every established configuration surface in the relevant milestone scope.

## Step 6: Human Plan Review Gate

When planning is complete, set the status to `AWAITING_HUMAN_PLAN_APPROVAL`. Tell the user:

1. Plan file path.
2. Intended approach and essential decisions.
3. Milestone summary, including local verification and approval boundary for each.
4. Blocking questions resolved and assumptions made.
5. Relevant constraints, risks, and `LEARNINGS.md` update status.
6. Ask for explicit approval to implement. A response such as `approved` or `continue` is sufficient.

Stop here. Do not implement in the same invocation.
