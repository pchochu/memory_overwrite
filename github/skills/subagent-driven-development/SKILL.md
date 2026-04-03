---
name: subagent-driven-development
description: Coordinate a two-phase development cycle - spec/plan first, then implement. Enforces strict separation between planning and coding.
---

# Subagent-Driven Development (SADD)

## Phase 1: Spec

- The **Planner** writes a comprehensive spec or implementation plan
  (objectives, file paths, function signatures, background from `MEMORY.md`).
- The **Orchestrator** reviews for completeness and approves.

## Phase 2: Implementation

- The **Coder** follows the approved spec using the `executing-plans` skill.
  No code outside the plan. May ask Orchestrator for clarification but must
  not change the spec independently.

## Phase 3: Spec Review

- The **Orchestrator** compares delivered work against the spec. Every
  requirement must be met, nothing extra added. Differences go back to Coder.

## Phase 4: Code Quality Review

- Request a code quality review using `code-review` skill. Reviewers focus on

  readability, performance, security, and adherence to project standards.

## Phase 5: Finish

- Use `finishing-a-development-branch` to prepare the PR.
  Do not merge directly into `main`, `master`, or `develop`.
