---
name: executing-plans
description: >-
  Execute an approved implementation plan step by step with verification
  after each step.
---

# Executing Plans

1. **Review the plan**: Read the plan file or instructions from the Planner.
   Understand each step's objective and required files.

2. **One step at a time**: For each step, open the relevant files, make the
   specified changes, and save. Do not combine multiple steps into one edit.

3. **Verify after each step**: Build the affected app using the commands in
   `AGENTS.md` Build Reference. If a step breaks the build, investigate and
   fix before proceeding.

4. **Handle discrepancies**: If the plan doesn't match the actual code
   structure, pause and consult the Orchestrator or Planner before improvising.

5. **Proceed sequentially**: Repeat until all steps are finished.
