---
name: executing-plans
message: >-
  Execute an approved implementation plan step by step.  After each step,
  verify the outcome and update memory as needed.
uses:
  - coder
  - fastcoder
  - memory
---

The **executing‑plans** skill guides the Coder or FastCoder through carrying
out a previously approved plan (often produced by the Planner). It emphasises
discipline: follow the plan exactly, verify after each step, and avoid
side‑quests.

**How to use this skill**

1. **Review the plan**: Read the plan file (e.g. `plan.md`) or instructions
   provided by the Planner. Understand each step’s objective and required
   files.
2. **Perform one step at a time**: For each step, open the relevant files in
   GitHub Copilot, make the specified changes, and save the file. Do not
   combine multiple steps into one edit.
3. **Verify after each step**: Run the relevant commands (e.g. `npm test`,
   `pnpm build`, or `cargo check`) to ensure the repository still compiles.  
   If a step breaks the build, investigate and fix the issue
   before proceeding.
4. **Update memory**: After completing a step, append a summary of the work to
   `.github/MEMORY.md`, including the file names changed and why. This helps
   other agents understand recent modifications.
5. **Proceed to the next step**: Repeat until all steps in the plan are
   finished. If you encounter a discrepancy between the plan and actual code
   structure, pause execution and consult the Orchestrator or Planner before
   improvising.

Use this skill whenever you have a detailed plan and need to execute it
efficiently without skipping important verification.
