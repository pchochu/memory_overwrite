---
name: finishing-a-development-branch
message: >-
  Finalise work on a feature branch.  Run the full test suite, summarise the
  work done, and present options to merge, create a pull request, or discard
  the branch.
uses:
  - orchestrator
  - coder
  - memory
---

The **finishing‑a‑development‑branch** skill helps ensure that completed work
is stable and integrated correctly. It is typically invoked by the
Orchestrator after all tasks are executed.

**How to use this skill**

1. **Run tests and build**: Execute the repository’s full test suite (e.g.
   `npm test`, `cargo test`, or `make verify`) and build the project. Fix any
   failing tests or build errors before proceeding.
2. **Review changes**: List all commits and file changes on the feature
   branch. Ensure that each change corresponds to a step in the plan or an
   agreed update. Check that there are no unrelated changes.
3. **Summarise work**: Create a concise summary describing what was
   implemented, any bugs fixed, and any new files or endpoints added. Update
   `.github/MEMORY.md` with this summary.
4. **Present options**: Provide the user or project maintainer with options:
   - **Merge to main**: If all tests pass and the feature is complete, merge
     the branch into `main` or open a pull request for code review.
   - **Keep branch open**: If further work is needed, keep the branch open and
     create follow‑up tasks.
   - **Discard branch**: If the work is no longer needed, delete the branch
     (using `git branch -D` and cleaning up any worktrees).
5. **Clean up**: After merging or discarding, remove any temporary git
   worktrees and branches associated with the task. Confirm that the working
   directory is clean.

Use this skill whenever you finish a feature branch or integration branch so
that the repository remains stable and the project history stays clear.
