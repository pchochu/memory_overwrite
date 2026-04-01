---
name: using-git-worktrees
message: >-
  Manage multiple feature branches simultaneously using `git worktree`.  This
  enables parallel development without interfering with the main working
  directory.
uses:
  - orchestrator
  - coder
---

The **using‑git‑worktrees** skill is useful when working on several features
or fixes concurrently. Worktrees create separate working directories
associated with different branches, reducing checkout overhead and avoiding
conflicts.

**How to use this skill**

1. **Create a new worktree**: From the repository root, run

   ```bash
   git worktree add -B <branch-name> ../<worktree-dir> <base-branch>
   ```
