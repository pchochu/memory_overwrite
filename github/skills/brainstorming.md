---
name: brainstorming
message: >-
  Explore the problem space and project context before committing to a specific
  solution. Use Socratic questioning to propose multiple designs or approaches,
  then wait for confirmation before writing any code.
uses:
  - memory
  - architecture
  - design
---

The **brainstorming** skill helps agents avoid wasted effort by pausing to
understand the goal, existing architecture, and prior decisions recorded in
`.github/MEMORY.md` and `.github/ARCHITECTURE.md`. It is usually used by the
Planner or Coder at the start of a task or when design decisions are unclear.

**How to use this skill**

1. **Clarify the goal**: Restate the objective in your own words and ask
   clarifying questions about scope, constraints, and success criteria. Check
   `.github/MEMORY.md`, `README.md`, and any relevant instruction files to
   understand previous decisions and architectural constraints.
2. **Research existing code**: Use GitHub Copilot to search the repository for
   related files, functions, or components. Summarise what already exists
   (e.g. available APIs, React components, or server utilities) that could be
   reused.
3. **Propose two to three approaches**: For each approach, briefly describe
   what you would build (e.g. which files to edit, what new functions to add)
   and highlight trade‑offs (complexity, reusability, performance). When
   relevant, include design considerations such as component hierarchy or
   accessibility.
4. **Seek approval**: Stop before writing any code. Present the proposed
   approaches to the user or Orchestrator and wait for feedback on which
   direction to pursue.
5. **Do not write to MEMORY.md by default**: A chosen design for a single task
   or ticket should **not** be recorded in `.github/MEMORY.md`. Only record
   something later if it becomes clear that the decision established a durable
   repository rule, reusable convention, or long-lived architectural constraint.

**Example**

> “I need to implement a new settings page. I see that there’s already a
> `SettingsForm` component under `apps/web/components`. I can either extend
> this component, create a new page that wraps it, or build an entirely new
> component if the requirements differ greatly. Option 1 reuses existing
> styles but may need refactoring; option 2 keeps code modular; option 3
> increases maintenance. Which approach would you like me to take?”

Use this skill whenever you start a new feature, when requirements are
ambiguous, or when design trade‑offs need to be evaluated.
