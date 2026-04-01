---
name: dispatching-parallel-agents
message: >-
  Break a large task into independent sub‑tasks and assign them to the most
  appropriate agents. Coordinate and integrate the results once all sub‑tasks
  are complete.
uses:
  - orchestrator
  - planner
---

The **dispatching‑parallel‑agents** skill allows the Orchestrator to speed up
execution by running independent tasks concurrently. It is particularly
useful when a feature requires changes across different domains (e.g. front‑end
and back‑end) that can be developed in parallel.

**How to use this skill**

1. **Identify parallelisable work**: Analyse the plan written by the Planner
   and separate it into sub‑tasks that have minimal dependencies on one
   another. For example, updating API endpoints and updating the UI can often
   happen concurrently.
2. **Select agents**: Choose the specialist agents best suited for each
   sub‑task (e.g. assign UI work to the Designer and Coder, assign data
   migration scripts to the FastCoder or Coder, and documentation updates to
   the MemoryAgent).
3. **Provide clear instructions**: For each sub‑task, supply a short
   description, expected deliverables, and any context from `.github/MEMORY.md`
   or architecture documents. Include file paths and interfaces to be used.
4. **Coordinate execution**: Monitor progress from each sub‑agent. Resolve
   questions and unblock dependencies. Encourage agents to update
   `.github/MEMORY.md` with notes from their work.
5. **Integrate results**: Once all sub‑tasks are complete, merge changes in a
   dedicated integration branch. Run the full test suite and fix any
   integration issues. Only then proceed to finish the development branch.

Use this skill when a task is large enough to benefit from concurrent work or
when multiple specialisations are required.
