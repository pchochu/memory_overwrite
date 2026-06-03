# Review Output Template

## Header

```markdown
# Code Review: <package_name> — <branch> → <target_branch>

- **Date**: <YYYY-MM-DD>
- **Repository**: <repo>
- **Package**: <package_name> (<package_path>)
- **Branch**: <current_branch>
- **Target**: <target_branch>
- **Package files changed**: <N>
- **External files affecting package**: <N>
- **Agents deployed**: <list of agent codenames>
- **Cross-domain items routed**: <count>
- **Uncertainties escalated**: <count>
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
2. <one-line summary of second>
3. <one-line summary of third>
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
- **Description**: <2-3 sentences>
- **Impact**: <what breaks or degrades>
- **Recommendation**: <specific actionable fix>
- **Cross-domain confirmation**: <if applicable>

---

## High Findings
<same format>

## Medium Findings
<same format>

## Low Findings
<same format>

## Nits

| ID | Title | File | Recommendation |
|----|-------|------|----------------|
| ... | ... | ... | ... |
```

## Agent Completion Status

```markdown
## Agent Completion Status

| Agent | Findings | Checklist | Cross-domain sent | Cross-domain received |
|-------|----------|-----------|-------------------|----------------------|
| component-arch | <N> | ✅ complete | <N> | <N> |
| react-patterns | <N> | ✅ complete | <N> | <N> |
| state-data | <N> | ✅ complete | <N> | <N> |
| bundle-performance | <N> | ✅ complete | <N> | <N> |
| error-resilience | <N> | ✅ complete | <N> | <N> |
| integration-impact | <N> | ✅ complete | <N> | <N> |
| <domain-specific> | <N> | ✅ complete | <N> | <N> |
```

## Deduplication Log

```markdown
## Deduplication Log

| Kept | Merged from | Reason |
|------|------------|--------|
| <finding ID kept> | <finding ID merged> | Same issue, kept higher severity |
```

## Uncertainties & Clarifications

```markdown
## Uncertainties & Clarifications

| # | Agent | Question | User Answer | Applied to |
|---|-------|----------|-------------|-----------|
| 1 | <agent> | <question> | <answer> | <finding ID> |
```
