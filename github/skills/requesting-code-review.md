---
name: requesting-code-review
message: >-
  Prepare your work for review by summarising changes, highlighting key
  decisions, and asking for feedback on specific aspects.
uses:
  - coder
  - orchestrator
---

The **requesting‑code‑review** skill provides a checklist for requesting a
review of completed work. It ensures reviewers have all the context they
need to evaluate the code against the plan and project standards.

**How to use this skill**

1. **Run tests and lint**: Ensure all tests pass and linter warnings are
   resolved. You should only request a review when the code is in a
   compilable state.
2. **Prepare a summary**: Describe what was implemented, referencing the
   approved plan steps. Note any deviations from the plan and explain
   why. Mention which files were created or updated.
3. **Highlight decisions**: Point out areas where you made design choices or
   trade‑offs. Provide links to relevant discussion in `.github/MEMORY.md` or
   issue comments.
4. **Ask for targeted feedback**: Specify if you need feedback on
   performance, security, adherence to architecture guidelines, or other
   aspects. This focuses the reviewer’s attention on what matters most.
5. **Tag reviewers**: Assign the appropriate reviewers (e.g. the
   Orchestrator, Planner, or subject‑matter expert). Make sure they have
   access to the branch or pull request.
6. **Update memory**: Append a note to `.github/MEMORY.md` summarising the
   request and the location of the pull request so future agents can find it.

Use this skill whenever you finish a piece of work and need someone else to
review it. A clear request speeds up the review process and increases the
chance of getting actionable feedback.
