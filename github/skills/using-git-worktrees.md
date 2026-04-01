---
name: using-git-worktrees
description: >-
  Manage multiple feature branches simultaneously using git worktree for
  parallel development.
---

# Using Git Worktrees

1. **Create a new worktree**:

   ```bash
   git worktree add -B <branch-name> ../<worktree-dir> <base-branch>
   ```

   Branch name must follow `AGENTS.md` convention:
   `{type}/{version}/{ticket}-{description}`
   (e.g. `feature/17/SME-1234-improve-eslint`)

2. **Perform work**: Open the worktree directory in your editor. All edits
   and commits happen there. Build and verify inside the worktree. Multiple
   worktrees can be active simultaneously.

3. **Manage worktrees**: Use `git worktree list` to view active worktrees.
   Navigate to the appropriate directory for each feature.
