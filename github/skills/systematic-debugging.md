---
name: systematic-debugging
message: >-
  Use a disciplined, root‑cause oriented approach to debug and fix issues.
uses:
  - coder
  - fastcoder
  - memory
---

The **systematic‑debugging** skill helps avoid random guessing and ensures
issues are truly resolved. It should be applied whenever tests fail,
unexpected behaviour appears, or bugs are reported.

**How to use this skill**

1. **Reproduce and understand**: Gather as much information as possible about
   the failure. Identify the expected behaviour, reproduction steps, and
   relevant files. Consult `.github/MEMORY.md` to see if similar issues were
   addressed before.
2. **Trace the root cause**: Use debugging tools, logging, and breakpoints to
   isolate where the failure occurs. Create a minimal reproduction if
   possible. Do not attempt to fix symptoms without finding the underlying
   problem.
3. **Design a fix**: Once the root cause is identified, propose the simplest
   change that resolves the issue without introducing new complexity. Update
   documentation or tests as necessary.
4. **Verify the fix**: Run the full test suite and manually test affected
   functionality. Ensure no regressions appear. Perform a code review if
   required.
5. **Update memory**: Record the root cause, the fix, and any lessons learned
   in `.github/MEMORY.md`. Include context such as why the bug occurred and
   how to avoid similar problems in the future.

Use this skill whenever you encounter bugs or failing tests. A systematic
approach reduces the chance of reintroducing issues and helps future agents
learn from past mistakes.
