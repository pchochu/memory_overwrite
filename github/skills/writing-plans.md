---
name: writing-plans
message: >-
  Create a detailed, step‑by‑step implementation plan that developers can
  follow.  Include file paths, functions to edit, commands to run, and links
  to relevant documentation.
uses:
  - planner
  - orchestrator
---

The **writing‑plans** skill defines how the Planner produces clear, actionable
plans for coding tasks. A good plan reduces ambiguity and enables agents to
execute quickly.

**How to use this skill**

1. **Understand the problem**: Read the user request, issue description, and
   existing memory entries. Clarify goals and acceptance criteria through
   brainstorming if necessary.
2. **Investigate the codebase**: Search the repository for relevant files,
   components, or APIs. Note where changes will need to occur.
3. **Outline tasks**: Break the solution into ordered steps. Each step
   should modify specific files or run specific commands. Include function
   names, directory paths, and any library APIs to use. Use bullet points for
   clarity.
4. **Add verification commands**: For steps that require testing or building,
   specify commands such as `npm test`, `pnpm lint`, or `cargo check`. This
   ensures executors know when to verify their work.
5. **Reference documentation**: Link to lines in `.github/MEMORY.md`,
   `.github/ARCHITECTURE.md`, or external docs that support design decisions.
6. **Review and refine**: Ensure the plan is complete and unambiguous. The
   Orchestrator should review it before execution. Iterate if necessary.
7. **Store the plan**: Save the plan in a file (e.g. `plan.md`) within the
   issue context or commit message so executors can refer to it.

Use this skill whenever you need to translate a feature request into specific
development steps. Clear plans empower the Coder and FastCoder to work
efficiently.
