---
name: finishing-a-development-branch
description:
  Finalise work on a feature branch — build, summarise, trigger review, and prepare a pull request.
  and prepare a pull request.
---

# Finishing a Development Branch

1. **Build the project**: Build all affected apps using `AGENTS.md` Build
   Reference. Fix any build errors before proceeding.

2. **Review changes**: List all commits and file changes on the branch.
   Ensure each change corresponds to a plan step or agreed update. Check
   for unrelated changes.

3. **Summarise work**: Create a concise summary of what was implemented,
   bugs fixed, and files changed — for the pull request description.

4. **Prepare the PR**: Open a pull request against the appropriate base
   branch. Ensure branch name follows the convention in `AGENTS.md`
   (e.g. `feature/17/SME-5689-improve-eslint`). Include the summary in
   the PR description. Do not merge directly into `main`, `master`, or
   `develop` locally.

5. **Trigger local PR review**: Before submitting for external review,
   spawn three dedicated review agents to evaluate changes in parallel.
   The Orchestrator collects feedback and integrates it before opening
   the final PR.
