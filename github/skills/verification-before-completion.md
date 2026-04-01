---
name: verification-before-completion
description: >-
  Verify that the solution works across all relevant dimensions before
  declaring a task finished.
---

# Verification Before Completion

1. **Build the affected app(s)**: Use the commands from `AGENTS.md` Build
   Reference. Common examples:

   ```bash
   # From Web/ directory
   pnpm --filter @mae/tasks build       # Studio + Management + Apps
   pnpm --filter @mae/dashboard build    # Dashboard
   pnpm --filter @mae/browsermap build   # BrowserMap
   pnpm --filter @mae/app-engine build   # App Engine
   ```

2. **Run lint**: Check for lint errors:

   ```bash
   pnpm --filter @mae/<package> eslint
   ```

3. **Manual verification**: If applicable, manually verify the feature in a
   local development environment. For UI work, check interactions,
   responsiveness, and accessibility.

4. **Update documentation**: Ensure new functions, APIs, or behaviour changes
   are documented in relevant docs. Update comments and type definitions.
