---
name: subagent-driven-development
message: >-
  Coordinate a two‑phase development cycle: first produce a detailed spec or
  plan, then implement it.  Enforce a strict separation between planning and
  coding, followed by spec and code quality reviews.
uses:
  - orchestrator
  - planner
  - coder
  - memory
---

The **subagent‑driven‑development** (SADD) skill accelerates development while
maintaining quality. It splits work into distinct phases performed by
specialised agents.

**How to use this skill**

1. **Spec phase**:
   - The **Planner** writes a comprehensive specification or implementation
     plan. This includes objectives, file paths, function signatures, and any
     necessary background from `.github/MEMORY.md` or architecture docs.
   - The **Orchestrator** reviews the spec for completeness and asks for
     revisions if information is missing or ambiguous. Once satisfied, the
     Orchestrator approves the spec.
2. **Implementation phase**:
   - The **Coder** follows the approved spec using the `executing-plans` skill.
     No code should be written outside the plan. The Coder may ask the
     Orchestrator for clarification but must not change the spec independently.
   - The **MemoryAgent** monitors the implementation, capturing important
     decisions, unexpected obstacles, and solutions in `.github/MEMORY.md`.
3. **Spec review**:
   - Once implementation is complete, the **Orchestrator** (or a dedicated
     reviewer) compares the delivered work against the spec. Check that every
     requirement is met and nothing extra was added. If differences exist,
     send the task back to the Coder for corrections.
4. **Code quality review**:
   - After spec compliance is confirmed, request a code quality review using
     the `requesting-code-review` skill. Reviewers focus on readability,
     performance, security, and adherence to project standards.
5. **Finish the branch**: Use the `finishing-a-development-branch` skill to
   integrate the work into the main branch once reviews are resolved.

Use this skill for non‑trivial features or refactors that benefit from a clear
specification phase and structured review cycle.
