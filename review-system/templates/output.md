# Review Output Template

## Header

```markdown
# Code Review: <branch> → <target_branch>

- **Date**: <YYYY-MM-DD>
- **Repository**: <repo>
- **Branch**: <current_branch>
- **Target**: <target_branch>
- **Diff stats**: <N> files changed, +<N>, -<N>
- **Agents deployed**: <list of agent codenames>
- **Cross-domain items routed**: <count>
- **Uncertainties escalated**: <count>
- **User clarifications received**: <count>
```

## Summary

```markdown
## Summary

| Severity | Count |
|----------|-------|
| CRITICAL | <N> |
| HIGH | <N> |
| MEDIUM | <N> |
| LOW | <N> |
| NIT | <N> |
| **Total** | **<N>** |

### Top 3 Risks
1. <one-line summary of most critical finding>
2. <one-line summary of second most critical finding>
3. <one-line summary of third most critical finding>
```

## Findings Section

```markdown
## Critical Findings

### [<AGENT_CODE>-<NNN>] <title>
- **Agent**: <agent name>
- **Severity**: CRITICAL
- **Confidence**: HIGH|MEDIUM|LOW
- **Evidence**:
  - `<file_path>:<line_range>`
  - `<file_path>:<line_range>`
- **Description**: <2-3 sentences explaining the issue>
- **Impact**: <what breaks or degrades>
- **Recommendation**: <specific actionable fix>
- **Cross-domain confirmation**: <if routed from another agent, note origin>

---

## High Findings
<same format>

---

## Medium Findings
<same format>

---

## Low Findings
<same format>

---

## Nits
<same format, can be more condensed>

| ID | Title | File | Recommendation |
|----|-------|------|----------------|
| ... | ... | ... | ... |
```

## Agent Completion Status

```markdown
## Agent Completion Status

| Agent | Findings | Checklist | Cross-domain sent | Cross-domain received |
|-------|----------|-----------|-------------------|----------------------|
| arch | <N> | ✅ complete | <N> | <N> |
| react-patterns | <N> | ✅ complete | <N> | <N> |
| runtime-safety | <N> | ✅ complete | <N> | <N> |
| dx-api | <N> | ✅ complete | <N> | <N> |
| bundle-performance | <N> | ✅ complete | <N> | <N> |
| state-data | <N> | ✅ complete | <N> | <N> |
| testing | <N> | ✅ complete | <N> | <N> |
| error-resilience | <N> | ✅ complete | <N> | <N> |
| maintainability | <N> | ✅ complete | <N> | <N> |
| migration-impact | <N> | ✅ complete | <N> | <N> |
```

## Deduplication Log

```markdown
## Deduplication Log

| Kept | Merged from | Reason |
|------|------------|--------|
| <finding ID kept> | <finding ID merged> | Same issue, <kept> had higher severity/more evidence |
```

## Uncertainties & Clarifications

```markdown
## Uncertainties & Clarifications

| # | Agent | Question | User Answer | Applied to |
|---|-------|----------|-------------|-----------|
| 1 | <agent> | <question text> | <user answer> | <finding ID> |
```
