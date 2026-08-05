---
description: Interview stakeholders and create a business-ready task definition for technical planning
agent: build
---

# Define Task: $ARGUMENTS

## Purpose

Produce a concise, implementation-neutral business definition that a human can review and `/plan-task` can later turn into a technical plan. Diagnose the problem and clarify the desired outcome; do not recommend architecture, code, libraries, APIs, data models, or implementation steps.

Follow the Problem Identification agent's interview method: ask exactly one question per response and wait for the answer before asking the next question. Do not present a list of questions.

## Step 1: Find Existing Context And Source

`$ARGUMENTS` may be a task identifier, tracker URL, title, or freeform problem statement.

Look in the repository-root `tasks/` directory for `task-[TASK_ID].definition.md`, matching by identifier or title slug. If found, read it completely and resume the missing, unclear, or unresolved parts instead of repeating confirmed information.

If a relevant tracker MCP tool is available and `$ARGUMENTS` identifies a task, load it and capture the identifier, title, description, URL, status, and existing acceptance criteria. Use this as interview context, not as a complete definition. If the tracker task is not found, tell the user: `Could not find task '$ARGUMENTS' in the available tracker. Verify the ID, or describe the problem directly.` Then stop.

If no tracker applies, use the supplied freeform information as the starting point. If it is insufficient to understand the problem, begin the interview.

## Step 2: Interview For A Business Definition

Gather only information needed to define the business problem and a reviewable outcome. Ask one focused question at a time, adapting to information already available. Prioritize these areas:

- Current state and root problem
- Users, stakeholders, and who is affected
- Business impact and desired outcome
- Clear, observable acceptance criteria
- Explicit in-scope and out-of-scope boundaries
- Business constraints: deadlines, policy, compliance, budget, rollout, ownership, or dependencies
- Known technical constraints supplied by stakeholders: required integrations, supported platforms, data boundaries, compatibility, performance, privacy, or security requirements
- Assumptions, unresolved decisions, and limitations

Challenge a proposed solution when it hides an unclear problem or conflicts with the stated outcome. Redirect the interview to the underlying need, but do not replace stakeholder decisions with technical recommendations.

Known technical constraints are externally imposed requirements, not codebase analysis. Do not inspect source code, infer project conventions, or prescribe a technical solution during this command.

When enough information is collected, summarize the proposed definition. Ask one final question to confirm it or collect corrections.

## Step 3: Create Or Update The Definition

Ensure the repository-root `tasks/` directory exists. Create or update `tasks/task-[TASK_ID].definition.md`; if no ID is available, derive a short kebab-case slug from the title.

Set status to `READY_FOR_TECHNICAL_PLANNING` only when the problem, desired outcome, acceptance criteria, in-scope items, out-of-scope items, and material limitations are clear. Otherwise set it to `DRAFT` and preserve the blocking open questions.

```markdown
# [TASK_ID]: [Task Title]

## Status
- Status: DRAFT / READY_FOR_TECHNICAL_PLANNING
- Source: [tracker URL or `freeform`]
- Created: [timestamp]
- Updated: [timestamp]

## Problem And Context
- Current state: [...]
- Problem: [...]
- Affected users or stakeholders: [...]
- Business impact: [...]
- Desired outcome: [...]

## Description
[Concise implementation-neutral narrative.]

## Acceptance Criteria
- [...]

## Scope
- In scope:
  - [...]
- Out of scope:
  - [...]

## Constraints And Limitations
- Business constraints: [...]
- Known technical constraints: [...]
- Dependencies: [...]

## Assumptions And Open Questions
- Assumption: [...]
- Open question: [...]
- Decision needed: [...]

## Decision Record
| Decision | Rationale | Source |
|---|---|---|
| [...] | [...] | [user / tracker / stakeholder] |

## Handoff Notes
- Ready for technical planning: yes / no
- Technical planning must preserve: [scope, criteria, constraints, and decisions]
```

Keep it concise. Do not include technical investigation, architecture options, implementation steps, generic project conventions, or internal agent reasoning.

## Step 4: Report

Tell the user:

1. Definition file path and status.
2. Problem, desired outcome, and scope summary.
3. Acceptance criteria and material constraints.
4. Open questions that prevent technical planning, if any.
5. If ready: `Run /plan-task $ARGUMENTS to create the technical plan.`
