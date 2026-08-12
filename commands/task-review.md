---
description: Review all current-branch changes against develop, using available task context when present
agent: dotnet-reviewer
---

# Task Review: $ARGUMENTS

Review the current branch as it stands. The implementation may be incomplete; assess the changes that exist now and do not require the task to be in a completed state.

## 1. Establish The Review Scope

1. Read the repository-root `AGENTS.md` and `LEARNINGS.md` when present, then any nearer `AGENTS.md` files governing changed areas.
2. Confirm that `develop` exists locally. If it does not, report the missing baseline and stop; do not substitute another branch.
3. Identify the common ancestor of `develop` and `HEAD`.
4. Review all committed branch changes with `git diff develop...HEAD`.
5. Also inspect staged and unstaged changes with `git diff --cached` and `git diff` so the review represents the current working state. Do not count duplicated hunks twice.
6. Use `git status --short` and `git diff --name-status develop...HEAD` to establish the changed-file inventory. Include relevant new, deleted, renamed, generated, configuration, migration, and test files.

Do not edit files, run formatting, or execute tests as part of this command. This is a read-only review.

## 2. Load Task Context When Available

`$ARGUMENTS` may be a task ID, slug, branch-related task reference, or optional reviewer note.

Look for matching files under the repository-root `tasks/` directory:

- `tasks/task-[TASK_ID].definition.md`
- `tasks/task-[TASK_ID].research.md`
- `tasks/task-[TASK_ID].plan.md`

When a matching task is found, read every available document completely. Use its goal, scope, acceptance criteria, approved decisions, milestone boundaries, technical implementation plan, constraints, and validation requirements to assess whether the changed code implements the intended work safely.

When no matching task context is found, continue the review based on the diff, repository guidance, and nearby code. Do not treat the absence of a task as a finding or a reason to stop.

## 3. Perform The Review

Review the complete scoped change set, not merely files named by task documents. Follow the `dotnet-reviewer` review order: Security, Correctness, Architecture, Performance, then Code Quality and Maintainability.

Inspect enough surrounding code, call sites, tests, configuration, dependency registration, persistence boundaries, API contracts, and migrations to validate each changed behavior. Pay particular attention to:

- Regressions caused by incomplete or partially integrated work.
- Differences between changed behavior and available task acceptance criteria or approved scope.
- Missing or inadequate tests for changed behavior and failure paths.
- Clean Architecture boundary violations and project-specific conventions.
- Security, authorization, validation, data-protection, and compatibility risks.

Do not report speculative concerns, pre-existing issues outside the scoped change set, or style preferences without a concrete impact. If a pre-existing condition makes a changed line unsafe, report it only when the change introduces or materially worsens the risk.

## Report Findings

### Output Format:
- Skip polite pleasantries and generic praise. Be direct, objective, and concise.
- List identified issues using bullet points.
- For each issue, you must provide exactly these three things:
  1.  **The Issue:** What is wrong (specify if it's an Architecture, Security, or Maintainability issue).
  2.  **The Reason:** Why it violates Clean Architecture, security best practices, or rules defined in `AGENTS.md`.
  3.  **The Fix:** A concise code snippet demonstrating the correct approach.
- If the code meets all standards, simply output: "The code meets the project's architectural, security, and maintainability standards."