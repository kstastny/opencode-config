---
description: Execute or resume one independently reviewed milestone from an approved task implementation plan
agent: build
---

# Task Implement: $ARGUMENTS

## Step 0: Load Applicable Guidance

Read the repository-root `AGENTS.md` and `LEARNINGS.md` when present, followed by any nearer `AGENTS.md` files governing the milestone's files. Apply relevant OpenCode skills.

`AGENTS.md` is the source for stable project conventions and validation commands. `LEARNINGS.md` is for durable, non-obvious project-specific constraints. During implementation, review, and validation, update `LEARNINGS.md` only for a recurring, stable fact that is not already documented in project guidance or nearby code. Keep additions to 1-3 dated bullets with a source; never add generic advice, temporary status, or unresolved guesses.

## Orchestrator Role

You coordinate one milestone at a time from the approved task plan through implementation, local verification, AI review, fixes, and human approval. Keep work focused on the selected milestone. Do not begin a dependent later milestone until the prior milestone has explicit human approval.

Use suitable OpenCode subagents for isolated research, implementation, or review when available. Do not invent agent types. The build agent may work directly when the change is small or tightly coupled.

When delegating, provide the milestone outcome, acceptance criteria, constraints, relevant areas, permission to edit or review only, verification expectations, and expected return format. Describe required behavior and essential constraints, not incidental implementation mechanics.

## Mandatory Milestone Loop

```text
1. Find and read the approved plan
2. Select the next eligible milestone
3. Implement only that milestone
4. Run its focused local verification
5. AI-review the milestone changes
6. Fix Critical and Warning findings, then re-run focused verification
7. Record results and pause for human review
8. Receive explicit human consent
9. Mark the milestone approved and repeat for the next eligible milestone
10. After final milestone approval, run full validation and complete the task
```

Passing tests never replace AI or human code review. Do not combine several planned milestones into one implementation wave merely because their files are related.

## Step 1: Find And Read The Approved Plan

Find `tasks/task-[TASK_ID].plan.md` at the repository root. Match `$ARGUMENTS` by task ID or slug. If no file exists, tell the user: `No implementation plan found for '$ARGUMENTS'. Run /task-plan $ARGUMENTS first to create the plan.` Then stop.

Read the complete plan. Check task status, all milestone statuses, dependencies, acceptance criteria, local verification, final validation, and execution record.

- If task status is `COMPLETED`, report that it is complete and stop.
- If task status is not `APPROVED_FOR_IMPLEMENTATION` or `IN_PROGRESS`, tell the user: `Implementation plan for '$ARGUMENTS' requires human approval. Run /task-plan $ARGUMENTS to review or approve it.` Then stop.
- If a milestone is `AWAITING_HUMAN_APPROVAL` and the user has not explicitly consented in the current conversation, report its implementation, local verification, AI review, and unresolved suggestions. Ask for consent and stop.
- Explicit human consent in the conversation is sufficient. Do not require a special command or flag. When consent is present, record it through Step 7. Do not infer approval merely because `/task-implement` was invoked again with a task ID.
- If an approved final milestone is awaiting final completion, proceed to final validation.

## Step 2: Select The Next Milestone

Select the first `PLANNED` milestone whose dependencies are approved. If none is eligible and no final validation is pending, report the blocking milestone or dependency and stop.

Set task status to `IN_PROGRESS`; set the selected milestone status to `IN_PROGRESS`; update the timestamp and execution record.

The milestone, not its file list, is the execution boundary. Within that boundary, group tightly coupled files together and use parallel work only for genuinely independent areas that do not create edit conflicts.

## Step 3: Implement The Milestone

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

Scope:
- [files or areas]

Verification:
- [focused local command or procedure]

Return: files changed, acceptance-criteria status, validation result, issues, and follow-up work.
```

Update the milestone scope if implementation requires an additional file. Record the reason. If the required change expands the milestone's outcome or invalidates its review boundary, stop and revise the plan with the user rather than silently adding work.

## Step 4: Focused Local Verification

Run the milestone's documented local verification before AI review. Use the project-prescribed command when available. If the plan has no clear command, perform the stated verification procedure; if neither is adequate, identify the missing verification and ask the user before proceeding.

If verification fails, fix only the milestone-related failure and re-run it. Do not proceed to AI review until it passes, or report a blocker clearly.

Record the exact command or procedure and result in the execution record.

## Step 5: AI Review

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

## Step 6: Human Review Gate

After local verification passes and AI review is complete, set the selected milestone status to `AWAITING_HUMAN_APPROVAL`. Update the execution record with:

- Files and behavior changed
- Acceptance-criteria result
- Exact local verification result
- AI review result and fixes
- Non-blocking suggestions or residual risks

Tell the user the milestone is ready for review and ask for explicit consent to continue. A simple conversational response such as `approved`, `continue`, or equivalent is sufficient. Stop here. Do not start the next milestone in the same invocation.

## Step 7: Record Human Approval

After the user explicitly consents, set that milestone status to `APPROVED`, update the timestamp and execution record with the approval, and then:

- If another milestone is eligible, begin it through Step 2.
- If this was the final milestone, proceed to final validation.

If the user requests changes, set the milestone status back to `IN_PROGRESS`, record the request, and repeat Steps 3-6.

## Step 8: Final Validation And Completion

Only after every milestone is `APPROVED`, run the plan's full required validation. Supplement it from applicable `AGENTS.md`, `LEARNINGS.md`, scripts, Makefiles, CI configuration, and project documentation when necessary.

If validation fails, fix the issue, re-run validation, then return the affected milestone to `AWAITING_HUMAN_APPROVAL` if the fix changes its reviewed code. Do not mark the task complete until the affected milestone is approved again.

When validation passes:

- Set task status to `COMPLETED`.
- Update timestamp and execution record with final validation results.
- Capture any qualifying durable learning in `LEARNINGS.md`.
- Report the implementation summary, files changed, per-milestone local verification and review results, final validation, `LEARNINGS.md` status, and a suggested project-format commit message.
