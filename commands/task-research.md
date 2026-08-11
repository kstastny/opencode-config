---
description: Investigate a defined task and create a human-reviewable technical research document
agent: build
---

# Task Research: $ARGUMENTS

## Purpose

Investigate the current technical situation for an approved business definition. Produce an evidence-based research document that a human reviews before design and planning begin. Understand the terrain; do not select an architecture or write an implementation plan.

## Step 1: Load Inputs And Guidance

Find `tasks/task-[TASK_ID].definition.md` at the repository root, matching `$ARGUMENTS` by task ID or slug. If no definition exists, tell the user: `No business definition found for '$ARGUMENTS'. Run /task-define $ARGUMENTS first.` Then stop.

Read the complete definition.

- If its status is `DRAFT`, report its open questions and tell the user: `Complete the business definition with /task-define $ARGUMENTS before researching.` Then stop.
- If its status is not `READY_FOR_RESEARCH`, report the status and stop unless it is an existing research handoff state for this task.

Read applicable project guidance before researching:

- Read the repository-root `AGENTS.md` if present, then any nearer `AGENTS.md` files governing areas the task may affect.
- Read the repository-root `LEARNINGS.md` if present, plus relevant local notes when they exist.
- Identify applicable OpenCode skills.

`AGENTS.md` is the source for stable project conventions. `LEARNINGS.md` contains durable, non-obvious project constraints. Update `LEARNINGS.md` only when a fact discovered during research is specific to the project, likely to recur, stable, and not already documented in `AGENTS.md` or nearby code. Keep additions to 1-3 dated bullets with a source. Never add temporary status, generic advice, or unresolved speculation.

## Step 2: Resume Or Start Research

Look for `tasks/task-[TASK_ID].research.md`.

- If it is `AWAITING_HUMAN_RESEARCH_APPROVAL` and the user has not explicitly approved it in the current conversation, report its key findings, open questions, and delivery-shape recommendation. Ask for approval and stop.
- Explicit conversational approval is sufficient. Do not infer approval merely because `/task-research` was invoked again.
- If it is `AWAITING_HUMAN_RESEARCH_APPROVAL` and the user has explicitly approved it, set status to `APPROVED_FOR_PLANNING`, update its timestamp and research record, and report: `Research approved. Run /task-plan $ARGUMENTS to create the implementation plan.` Then stop.
- If it is already `APPROVED_FOR_PLANNING`, report that it is approved and stop.
- If it is `IN_PROGRESS` or `NEEDS_REVISION`, read it completely and continue only the missing investigation.

## Step 3: Investigate The Current Situation

Inspect only what is needed to establish the task's current behavior, constraints, dependencies, risks, and validation options. Follow applicable `AGENTS.md` guidance first.

Use targeted investigation:

- Read one to three analogous implementations where they establish a relevant pattern or boundary.
- Read relevant tests, configuration surfaces, integration boundaries, and data flows when they affect the task.
- Trace relationships and dependencies needed to understand the requested outcome.
- Check high-risk requirements when relevant: identity, uniqueness, relationships, data types, search, performance, configuration, dependencies, security, compatibility, privacy, migration, and rollout.

Record claims with source paths. Do not produce a generic convention inventory, inspect unrelated layers, choose a design, or prescribe implementation mechanics.

## Step 4: Resolve Blocking Research Questions

Classify uncertainty:

- **Blocking question:** must be answered before a responsible plan can be created because it affects external behavior, data shape, security, compatibility, cost, rollout, or safe validation.
- **Research finding:** evidence about the existing system; record its sources.
- **Planning decision:** defer to `/task-plan` when multiple sound approaches remain and the business outcome is unaffected.

Use the `question` tool only for blocking questions. State the evidence, the decision needed, available options, recommended option, and consequence. Record the answer and resulting decision in the research document.

If a new answer changes the business outcome, scope, or acceptance criteria, stop and direct the user to update the definition with `/task-define $ARGUMENTS` before continuing.

## Step 5: Create Or Update The Research Document

Ensure `tasks/` exists at the repository root. Create or update `tasks/task-[TASK_ID].research.md`. Set its status to `AWAITING_HUMAN_RESEARCH_APPROVAL` only when all blocking research questions are resolved. Otherwise set it to `NEEDS_REVISION` and preserve the blockers.

```markdown
# [TASK_ID]: [Task Title] - Technical Research

## Status
- Status: IN_PROGRESS / NEEDS_REVISION / AWAITING_HUMAN_RESEARCH_APPROVAL / APPROVED_FOR_PLANNING
- Business definition: `tasks/task-[TASK_ID].definition.md`
- Created: [timestamp]
- Updated: [timestamp]

## Research Scope
- Question investigated: [what current technical situation was examined]
- Preserved requirements: [concise scope, criteria, and constraints from definition]
- Exclusions: [what was intentionally not investigated]

## Current Technical Landscape
- Finding: [...]
  - Sources: `path/to/file:line`, [...]
- Finding: [...]
  - Sources: [...]

## Applicable Constraints And Conventions
- [Task-relevant constraint or convention]
  - Source: `path/to/file:line` / `AGENTS.md` / `LEARNINGS.md`

## Relevant Components And Dependencies
- [Component, integration, configuration surface, or test area]: [relationship to task]
  - Sources: [...]

## Risks And Validation Considerations
- Risk: [...]
  - Impact: [...]
  - Evidence: [...]
- Validation consideration: [...]

## Questions And Resolutions
- Blocking question: [...]
  - Answer: [...]
  - Decision: [...]
  - Source: [user / stakeholder / tracker]
- Planning decision to make: [...]

## Delivery Shape Recommendation
- Recommendation: single implementation unit / milestones likely required / decide during planning
- Rationale: [...]
- Suggested boundaries or sequencing risks: [...]

## Research Record
- [timestamp] Research created or updated.
- [timestamp] [question resolved, review requested, or approval recorded]
```

The research document is factual preparation for planning. Do not include a selected architecture, implementation steps, exhaustive file-change list, or internal agent reasoning.

## Step 6: Human Research Review Gate

When research is complete, set the status to `AWAITING_HUMAN_RESEARCH_APPROVAL`. Tell the user:

1. Research file path.
2. Current-system findings and their sources.
3. Relevant constraints, risks, and validation considerations.
4. Blocking questions resolved or still open.
5. Delivery-shape recommendation.
6. Ask for explicit approval to proceed to planning. A response such as `approved` or `continue` is sufficient.

Stop here. Do not create a plan in the same invocation.
