# Review Output Template


## Output Rules

```markdown
- Schema only. No preamble. No "here is". No closing summary.
- One-line fields unless evidence needs multiple lines.
- Do not paste code except minimal fix sketch.
- Empty optional sections omitted; required empty sections use `None`.
- Keep descriptions to max 2 sentences.
```

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
- **Checklist items skipped as N/A**: <count>
- **Routed items dismissed**: <count>
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

### Merge Decision
- **Recommendation**: BLOCK|ALLOW_WITH_FIXES|ALLOW
- **Blocking findings**: <IDs or none>
- **Highest user/task impact**: <summary or N/A>
- **Required validation before merge**: <commands/manual checks>
```

## Action Plan

```markdown
## Action Plan

| Priority | Finding | Owner | Fix size | Must fix before merge? | Validation |
|----------|---------|-------|----------|------------------------|------------|
| P0|P1|P2 | <finding ID> | <team/person or TBD> | XS|S|M|L | YES|NO | <command/manual check> |
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
- **Description**: <max 2 sentences explaining the issue>
- **Impact**: <what breaks or degrades>
- **UX severity inputs**: <frequency/task criticality/impact/persistence/affected segment/workaround, or N/A for non-user-facing findings>
- **Recommendation**: <specific actionable fix>
- **Acceptance criteria**: <how to know the issue is fixed>
- **Suggested validation**: <command/manual check>
- **Fix size**: XS|S|M|L
- **Merge blocking**: YES|NO
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

| Agent | Findings | Applicability gate | Checklist | Cross-domain sent | Cross-domain received |
|-------|----------|--------------------|-----------|-------------------|----------------------|
| arch | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| react-patterns | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| runtime-safety | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| dx-api | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| bundle-performance | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| state-data | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| testing | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| error-resilience | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| maintainability | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| migration-impact | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
| ripple-effect | <N> | ✅ complete | ✅ complete/N/A justified | <N> | <N> |
```

## Routed Items Log

```markdown
## Routed Items Log

| Source | Target | Routing reason | Decision | Final finding | Dismissal reason |
|--------|--------|----------------|----------|---------------|------------------|
| <agent finding ID> | <agent> | <reason> | CONFIRM|ADJUST|DISMISS | <finding ID or N/A> | <if dismissed> |
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

| # | Agent | Blocking? | Question | Default assumption | User Answer | Applied to |
|---|-------|-----------|----------|--------------------|-------------|-----------|
| 1 | <agent> | YES|NO | <question text> | <assumption if unanswered> | <user answer or N/A> | <finding ID> |
```
