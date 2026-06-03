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
- **Downstream consumers identified**: <N>
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

### Semver Assessment
- **Recommended bump**: patch | minor | major
- **Breaking changes**: <count>
- **New features**: <count>
- **Bug fixes**: <count>

### Top 3 Risks
1. <one-line summary>
2. <one-line summary>
3. <one-line summary>
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
- **Impact**: <what breaks for consumers>
- **Affected consumers**: <list of packages that consume this export>
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
| api-surface | <N> | ✅ complete | <N> | <N> |
| type-safety | <N> | ✅ complete | <N> | <N> |
| downstream-impact | <N> | ✅ complete | <N> | <N> |
| bundle-treeshaking | <N> | ✅ complete | <N> | <N> |
| testing | <N> | ✅ complete | <N> | <N> |
| compat | <N> | ✅ complete | <N> | <N> |
| ogc-style | <N> | ✅ complete | <N> | <N> |
```

## Downstream Impact Summary

```markdown
## Downstream Impact Summary

| Changed Export | Consumers | In Diff? | Risk |
|---------------|-----------|----------|------|
| <symbol> | <package1>, <package2> | partial | HIGH |
| ... | ... | ... | ... |
```

## Deduplication Log

```markdown
## Deduplication Log

| Kept | Merged from | Reason |
|------|------------|--------|
| <ID kept> | <ID merged> | Same issue, kept higher severity |
```

## Uncertainties & Clarifications

```markdown
## Uncertainties & Clarifications

| # | Agent | Question | User Answer | Applied to |
|---|-------|----------|-------------|-----------|
| 1 | <agent> | <question> | <answer> | <finding ID> |
```
