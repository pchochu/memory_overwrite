---
name: systematic-debugging
description: Disciplined, root-cause oriented approach to debug and fix issues.
---

# Systematic Debugging

1. **Reproduce and understand**: Gather information about the failure —
   expected behaviour, reproduction steps, relevant files. Check `MEMORY.md`
   for similar past issues.

2. **Trace the root cause**: Use debugging tools, logging, and breakpoints
   to isolate where the failure occurs. Create a minimal reproduction if
   possible. Do not fix symptoms without finding the underlying problem.

3. **Design a fix**: Propose the simplest change that resolves the issue
   without introducing new complexity.

4. **Verify the fix**: Build the affected app(s) using the commands in
   `AGENTS.md` Build Reference. Confirm the issue is resolved and no
   regressions appear.
