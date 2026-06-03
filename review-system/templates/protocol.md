# Agent Communication Protocol

## Message Types

### INITIAL_REVIEW (Orchestrator → Agent)

Sent at the start of a review pass. Contains all neutral context.

```
[ORCHESTRATOR → AGENT:<codename>]
Type: INITIAL_REVIEW

## Context
<Neutral Context Brief — see orchestrator.md>

## Your Instructions
<Full content of the agent's .md file>

## Constraints
- Review ONLY the changed files (diff scope).
- Stay within your domain. Tag out-of-scope findings with [CROSS-DOMAIN: <target-agent>].
- If uncertain about severity, scope, or interpretation, tag with [UNCERTAIN: <question>].
- Complete the Applicability Gate before deep review. Run applicable checklist items; mark non-applicable items N/A with evidence-backed rationale.
- Do not speculate. Base findings on evidence you can cite with file:line.
- Do not assume intent. If code seems intentional but problematic, note both possibilities.
- For user-facing findings, include UX severity inputs: frequency, task criticality, impact, persistence, affected segment, and workaround.
- For uncertainty, mark whether the question is blocking. Non-blocking uncertainty must state the default assumption and confidence instead of interrupting the user.

## Expected Output
Follow the Output Format section in your agent instructions exactly.
```

### CROSS_DOMAIN_ITEM (Orchestrator → Agent)

Sent when another agent forwards a finding into this agent's domain.

```
[ORCHESTRATOR → AGENT:<codename>]
Type: CROSS_DOMAIN_ITEM

## Origin
- From agent: <source_agent_codename>
- Finding ID: <source_finding_id>
- Routing reason: <why source agent believes this belongs to target agent>
- Requested decision: CONFIRM|ADJUST|DISMISS
- Routing pass: 1 of 1

## Item
<verbatim finding text from source agent>

## Routing Metadata
```yaml
cross_domain:
  source_agent: <source_agent_codename>
  target_agent: <target_agent_codename>
  source_finding_id: <source_finding_id>
  reason: <specific technical reason>
  requested_decision: CONFIRM|ADJUST|DISMISS
  evidence:
    - <file:line>
```

## Task
Evaluate this finding within YOUR domain expertise:
1. Confirm the finding (assign your own severity + evidence)
2. Adjust severity with justification
3. Dismiss with justification (must be technical, not opinion)

## Expected Output
```markdown
### Cross-domain evaluation: <source_finding_id>
- Decision: CONFIRM|ADJUST|DISMISS
- Severity (yours): <level or N/A if dismissed>
- Justification: <technical reasoning>
- Additional evidence: <if any>
- Final disposition: CREATE_FINDING|MERGE_WITH_SOURCE|DISMISS_FROM_FINDINGS
```
```

### CLARIFICATION_RESPONSE (Orchestrator → Agent)

Sent when the user answers an uncertainty question.

```
[ORCHESTRATOR → AGENT:<codename>]
Type: CLARIFICATION_RESPONSE

## Original question
<agent's question verbatim>

## User's answer
<user's response verbatim>

## Task
Incorporate this information into your finding. Update severity/recommendation if needed.

## Expected Output
```markdown
### Clarification applied: <finding_id>
- Updated severity: <if changed>
- Updated recommendation: <if changed>
- Rationale: <how user's answer affected assessment>
```
```

### REVIEW_COMPLETE (Agent → Orchestrator)

The standard review output following the agent's Output Format.

### CROSS_DOMAIN_FORWARD (Agent → Orchestrator)

Embedded within findings using the tag plus structured metadata:
```markdown
[CROSS-DOMAIN: <target_agent_codename>]
```

```yaml
cross_domain:
  source_agent: <source_agent_codename>
  target_agent: <target_agent_codename>
  source_finding_id: <source_finding_id>
  reason: <specific technical reason this target owns the decision>
  requested_decision: CONFIRM|ADJUST|DISMISS
  evidence:
    - <file:line>
```

Rules:
- Route only when the issue materially depends on another domain's expertise.
- Do not route preferences or low-confidence guesses. Use uncertainty metadata instead.
- One routed item receives one follow-up pass. Further routing requires user approval.

### UNCERTAINTY_ESCALATION (Agent → Orchestrator)

Embedded within findings using the tag and uncertainty metadata:
```
[UNCERTAIN: <specific technical question that needs user clarification>]
```

```yaml
uncertainty:
  blocking: YES|NO
  question: <specific answerable question>
  default_assumption_if_unanswered: <assumption>
  impact_if_wrong: <what would change>
  confidence_without_answer: HIGH|MEDIUM|LOW
```

Blocking uncertainty is escalated to the user. Non-blocking uncertainty remains in the report with the stated assumption and confidence.

---

## Rules

1. All communication is technical. No conversational filler.
2. Orchestrator passes information verbatim. No summarization, no interpretation.
3. Agents must not reference other agents' work (they don't have access).
4. Cross-domain items are evaluated independently — the target agent may disagree.
5. Uncertainty questions must be specific and answerable (not "is this okay?").
6. Non-blocking uncertainty must not interrupt the user.
7. Cross-domain routing must include structured metadata and has a maximum depth of one follow-up pass unless approved by the user.
8. All findings must cite evidence. No evidence = not a finding.
