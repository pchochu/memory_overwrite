---
name: verification-before-completion
description: Verify that the solution works across all relevant dimensions before declaring a task finished.
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

4. **Ask the user to test using the seamless agent**: Before declaring the
   task complete, **always** use the `vscode_askQuestions` (seamless ask) tool
   to prompt the user to manually test the fix or feature in their environment.
   Do **not** simply mention this in plain response text — the tool call is
   mandatory. Do not finish or hand off until the user confirms via that
   prompt that the behaviour is correct.

   Example question to ask via the tool:

   > "The build passes. Could you please test this manually by reproducing the
   > original steps and confirm whether the fix works as expected?"

   Only proceed to wrap up after the user responds with explicit confirmation.

5. **Update documentation**: Ensure new functions, APIs, or behaviour changes
   are documented in relevant docs. Update comments and type definitions.
