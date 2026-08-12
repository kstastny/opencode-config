---
description: Execute or resume one independently reviewed milestone from an approved task implementation plan
agent: build
---

# Task Implement: $ARGUMENTS

## Step 0: Load Applicable Guidance

Read the repository-root `AGENTS.md` and `LEARNINGS.md` when present, followed by any nearer `AGENTS.md` files governing the milestone's files. Apply relevant OpenCode skills.

`AGENTS.md` is the source for stable project conventions and validation commands. `LEARNINGS.md` is for durable, non-obvious project-specific constraints. During implementation, review, and validation, update `LEARNINGS.md` only for a recurring, stable fact that is not already documented in project guidance or nearby code. Keep additions to 1-3 dated bullets with a source; never add generic advice, temporary status, or unresolved guesses.

## Orchestrator Role

You coordinate one milestone at a time from the approved task plan through technical implementation planning, human approval of that approach, implementation, local verification, AI review, fixes, and human approval. Keep work focused on the selected milestone. Do not begin a dependent later milestone until the prior milestone has explicit human approval.

Use suitable OpenCode subagents for isolated research, implementation, or review when available. Do not invent agent types. The build agent may work directly when the change is small or tightly coupled.

When delegating, provide the milestone outcome, acceptance criteria, constraints, relevant areas, permission to edit or review only, verification expectations, and expected return format. Describe required behavior and essential constraints, not incidental implementation mechanics.

## Mandatory Milestone Loop

```text
1. Find and read the approved plan
2. Select the next eligible milestone
3. Create a concrete technical implementation plan for that milestone
4. Pause for human approval of the technical approach
5. Implement only the approved technical plan
6. Run its focused local verification
7. AI-review the milestone changes
8. Fix Critical and Warning findings, then re-run focused verification
9. Record results and pause for human review
10. Receive explicit human consent
11. Mark the milestone approved and repeat for the next eligible milestone
12. After final milestone approval, run full validation and complete the task
```

Passing tests never replace human review of the technical approach, AI review, or human code review. Do not combine several planned milestones into one implementation wave merely because their files are related.

## Step 1: Find And Read The Approved Plan

Find `tasks/task-[TASK_ID].plan.md` at the repository root. Match `$ARGUMENTS` by task ID or slug. If no file exists, tell the user: `No implementation plan found for '$ARGUMENTS'. Run /task-plan $ARGUMENTS first to create the plan.` Then stop.

Read the complete plan. Check task status, all milestone statuses, dependencies, acceptance criteria, local verification, final validation, and execution record.

- If task status is `COMPLETED`, report that it is complete and stop.
- If task status is not `APPROVED_FOR_IMPLEMENTATION` or `IN_PROGRESS`, tell the user: `Implementation plan for '$ARGUMENTS' requires human approval. Run /task-plan $ARGUMENTS to review or approve it.` Then stop.
- If a milestone is `AWAITING_HUMAN_IMPLEMENTATION_PLAN_APPROVAL` and the user has not explicitly consented in the current conversation, report its technical implementation plan, affected modules and files, risks, validation, and unresolved questions. Ask for consent and stop.
- If a milestone is `AWAITING_HUMAN_IMPLEMENTATION_PLAN_APPROVAL` and the user has explicitly consented in the current conversation, set its status to `IN_PROGRESS`, update the timestamp and execution record with the technical-plan approval, and continue with Step 4.
- If a milestone is `AWAITING_HUMAN_APPROVAL` and the user has not explicitly consented in the current conversation, report its implementation, local verification, AI review, and unresolved suggestions. Ask for consent and stop.
- Explicit human consent in the conversation is sufficient. Do not require a special command or flag. When consent is present, record the technical-plan approval through Step 4 or the implementation approval through Step 8. Do not infer approval merely because `/task-implement` was invoked again with a task ID.
- If an approved final milestone is awaiting final completion, proceed to final validation.

## Step 2: Select The Next Milestone

Select the first `PLANNED` milestone whose dependencies are approved. If none is eligible and no final validation is pending, report the blocking milestone or dependency and stop.

Set task status to `IN_PROGRESS`; set the selected milestone status to `TECHNICAL_PLANNING`; update the timestamp and execution record.

The milestone, not its file list, is the execution boundary. Within that boundary, group tightly coupled files together and use parallel work only for genuinely independent areas that do not create edit conflicts.

## Step 3: Create The Technical Implementation Plan

Before editing code, investigate the selected milestone enough to propose a concrete, reviewable implementation approach. Read the relevant modules, project or assembly structure, analogous implementations, tests, configuration, and integration boundaries. Do not make code, configuration, migration, generated-output, or external-data changes during this step.

Add a `#### Technical Implementation Plan` section under the selected milestone in `tasks/task-[TASK_ID].plan.md` with:

```markdown
#### Technical Implementation Plan
- Planned approach: [how the milestone will be implemented and how components interact]

- Planned changes:
  | Action | Path / module | Change |
  |---|---|---|
  | Create / Modify / Remove | `path/to/file-or-module` | [specific responsibility or change] |

- Component and contract changes:
  - [project, assembly, executable, API, configuration, data, or integration contract]

- Generalization assessment:
  - Reuse / introduce / avoid [abstraction]: [evidence, alternatives, and rationale]

- Risks and mitigations:
  - [correctness, compatibility, migration/data, security/privacy, performance, rollout, or operational risk]: [mitigation]
  - Not applicable: [risk area and why]

- Validation mapping:
  - [acceptance criterion]: `[focused command]` / [verification procedure]

- Scope boundary:
  - Included: [...]
  - Explicitly excluded: [...]
  - Plan revision required if: [...]
```

Use actual paths and module names when the repository establishes them. Mark a path as an estimate only when targeted investigation cannot determine it. For tools, integrations, migrations, or executables, state the name, location, invocation or entry point, inputs, outputs, error behavior, and affected projects or assemblies.

Do not create speculative abstractions. Generalize only when the codebase or approved task shows a concrete second use, a stable extension boundary, or an existing convention that requires it. Otherwise prefer the smallest task-specific implementation and record why.

If the technical approach changes the milestone outcome, accepted contract, or approved scope, stop and revise `/task-plan` with the user. If it only makes the approved outcome concrete, record it here for approval.

Set the milestone status to `AWAITING_HUMAN_IMPLEMENTATION_PLAN_APPROVAL`, update the execution record, report the technical plan, and ask for explicit approval. Stop here. Do not implement in the same invocation.

## Step 4: Implement The Milestone

Implement or delegate only the selected milestone. Follow applicable project guidance and existing patterns. Keep any required tests and configuration changes within the milestone that introduces the behavior.

Use this subagent prompt shape when useful:

```markdown
## [TASK_ID] - [MILESTONE_TITLE]

You may [edit code / only research / only review].

Outcome:
- [milestone outcome]

Acceptance criteria:
- [...]

Constraints and references:
- [applicable AGENTS.md, LEARNINGS.md, skills, research findings, or analogous files]

Approved technical implementation plan:
- [planned approach, concrete paths/modules, contracts, scope boundary, and risks]

Verification:
- [focused local command or procedure]

Return: files changed, acceptance-criteria status, validation result, issues, and follow-up work.
```

Implement only the approved technical implementation plan. If implementation requires an additional file, assembly, contract, approach, or material risk mitigation not described in that plan, stop, update the technical plan, return the milestone to `AWAITING_HUMAN_IMPLEMENTATION_PLAN_APPROVAL`, and request approval. If it expands the milestone outcome or invalidates its review boundary, revise `/task-plan` with the user instead.

## Step 5: Focused Local Verification

Run the milestone's documented local verification before AI review. Use the project-prescribed command when available. If the plan has no clear command, perform the stated verification procedure; if neither is adequate, identify the missing verification and ask the user before proceeding.

If verification fails, fix only the milestone-related failure and re-run it. Do not proceed to AI review until it passes, or report a blocker clearly.

Record the exact command or procedure and result in the execution record.

## Step 6: AI Review

Review only the files and behavior changed for the selected milestone. Use the most suitable available review subagent; otherwise perform a direct review.

```markdown
Review this milestone for bugs, security issues, convention violations, performance risks, maintainability concerns, and missing tests.

Task plan: tasks/task-[TASK_ID].plan.md
Technical research: tasks/task-[TASK_ID].research.md
Milestone: [MILESTONE_TITLE]
Outcome and acceptance criteria:
- [...]

Changed files:
- [...]

Return findings ordered by severity. For each include severity (Critical / Warning / Suggestion), dimension, file:line, explanation, and concrete fix guidance. State explicitly when no issues are found and identify residual testing risks.
```

Fix all Critical and Warning findings within the milestone. Record Suggestions without treating them as blocking. After any fix, re-run the focused local verification. Record review findings, fixes, and verification result.

If a finding requires changing a previous approved milestone's contract or expanding the current milestone, stop and request a plan revision or user decision.

## Step 7: Human Review Gate

After local verification passes and AI review is complete, set the selected milestone status to `AWAITING_HUMAN_APPROVAL`. Update the execution record with:

- Files and behavior changed
- Acceptance-criteria result
- Exact local verification result
- AI review result and fixes
- Non-blocking suggestions or residual risks

Tell the user the milestone is ready for review and ask for explicit consent to continue. A simple conversational response such as `approved`, `continue`, or equivalent is sufficient. Stop here. Do not start the next milestone in the same invocation.

## Step 8: Record Human Approval

After the user explicitly consents, set that milestone status to `APPROVED`, update the timestamp and execution record with the approval, and then:

- If another milestone is eligible, begin it through Step 2.
- If this was the final milestone, proceed to final validation.

If the user requests changes, set the milestone status back to `IN_PROGRESS`, record the request, and repeat Steps 4-7. If the requested change alters the approved technical approach, return to Step 3 and obtain technical-plan approval again.

## Step 9: Final Validation And Completion

Only after every milestone is `APPROVED`, run the plan's full required validation. Supplement it from applicable `AGENTS.md`, `LEARNINGS.md`, scripts, Makefiles, CI configuration, and project documentation when necessary.

If validation fails, fix the issue, re-run validation, then return the affected milestone to `AWAITING_HUMAN_APPROVAL` if the fix changes its reviewed code. Do not mark the task complete until the affected milestone is approved again.

When validation passes:

- Set task status to `COMPLETED`.
- Update timestamp and execution record with final validation results.
- Capture any qualifying durable learning in `LEARNINGS.md`.
- Report the implementation summary, files changed, per-milestone local verification and review results, final validation, `LEARNINGS.md` status, and a suggested project-format commit message.
