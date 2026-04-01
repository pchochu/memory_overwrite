---
name: receiving-code-review
message: >-
  Systematically process feedback from a code review, classify issues by
  severity, and act on them before continuing.
uses:
  - coder
  - fastcoder
  - orchestrator
---

The **receiving‑code‑review** skill ensures that code reviews are taken
seriously and feedback is incorporated appropriately. It applies to any
agent whose work is being reviewed, usually the Coder or FastCoder.

**How to use this skill**

1. **Read all comments**: Read the full diff and review comments on your pull
   request. Do not skim — you might miss critical points.
2. **Classify issues**:
   - **Critical**: Missing features, failing tests, security issues, or
     violations of the architecture and memory guidelines. These must be
     addressed before merging.
   - **Important**: Performance concerns, readability, or maintainability
     suggestions. These should be addressed unless there is a compelling
     reason not to (in which case explain why in a reply).
   - **Nitpick**: Stylistic issues or minor preferences. Address them if
     trivial; otherwise, acknowledge but defer if they don’t affect
     correctness.
3. **Fix critical and important issues**: Modify the code according to the
   reviewer’s suggestions. Run tests after each fix to ensure no regressions.
4. **Respond to comments**: For each comment, either commit a fix or explain
   why the suggestion is not adopted. Be polite and provide references from
   `.github/MEMORY.md` or coding guidelines when appropriate.
5. **Update memory**: Summarise any substantial changes you made due to the
   review in `.github/MEMORY.md` along with the reasons. This helps future
   agents avoid similar issues.
6. **Request re‑review**: Once all critical and important issues are resolved,
   notify the reviewer and request a final review.

Use this skill whenever a pull request receives feedback. Thoroughly
incorporating review comments improves code quality and fosters trust among
collaborators.
