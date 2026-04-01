---
name: code-review
description: >-
  Prepare work for review and systematically process feedback.
  Covers both requesting and receiving code reviews.
---

# Code Review

## Requesting a Review

1. **Build and lint**: Ensure the project builds and lints cleanly using
   the commands in `AGENTS.md` Build Reference. Only request review when
   the code compiles without errors.

2. **Prepare a summary**: Describe what was implemented, referencing the
   approved plan steps. Note deviations and explain why. List files
   created or updated.

3. **Highlight decisions**: Point out design choices or trade-offs.
   Reference relevant `MEMORY.md` entries or issue comments.

4. **Ask for targeted feedback**: Specify if you need feedback on
   performance, security, architecture adherence, or other aspects.

5. **Tag reviewers**: Assign the Orchestrator, Planner, or subject-matter
   expert as appropriate.

---

## Receiving Feedback

1. **Read all comments**: Read the full diff and review comments carefully.

2. **Classify issues**:
   - **Critical**: Missing features, build failures, security issues,
     architecture violations. Must be addressed.
   - **Important**: Performance, readability, maintainability suggestions.
     Address unless there is a compelling reason not to.
   - **Nitpick**: Stylistic preferences. Address if trivial; otherwise
     acknowledge and defer.

3. **Fix critical and important issues**: Modify code, rebuild the project
   to confirm it still compiles.

4. **Respond to comments**: For each comment, either commit a fix or explain
   why the suggestion is not adopted.

5. **Request re-review**: Once critical and important issues are resolved,
   notify the reviewer.
