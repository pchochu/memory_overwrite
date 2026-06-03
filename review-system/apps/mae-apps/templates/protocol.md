# Agent Communication Protocol

## Message Types

### INITIAL_REVIEW (Orchestrator → Agent)

Sent at the start of a review pass.

```
[ORCHESTRATOR → AGENT:<codename>]
Type: INITIAL_REVIEW

## Context
<Scoped Context Brief — see orchestrator.md>

## Your Instructions
<Full content of the agent's .md file>

## Scope Rules
- Review the FULL diff but report findings ONLY for:
  (a) Files within this package, OR
  (b) External changes that provably affect this package
- External files may be cited as EVIDENCE but are not standalone findings
- Stay within your domain. Tag out-of-scope findings with [CROSS-DOMAIN: <target-agent>].
- If uncertain about severity or scope, tag with [UNCERTAIN: <question>].
- Complete ALL checklist items. Mark N/A if not applicable with justification.
- Base findings on evidence you can cite with file:line.

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

## Item
<verbatim finding text from source agent>

## Task
Evaluate this finding within YOUR domain expertise:
1. Confirm (assign your own severity + evidence)
2. Adjust severity with justification
3. Dismiss with justification (must be technical)

## Expected Output
### Cross-domain evaluation: <source_finding_id>
- Decision: CONFIRM|ADJUST|DISMISS
- Severity (yours): <level>
- Justification: <technical reasoning>
- Additional evidence: <if any>
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
Incorporate this information. Update severity/recommendation if needed.

## Expected Output
### Clarification applied: <finding_id>
- Updated severity: <if changed>
- Updated recommendation: <if changed>
- Rationale: <how user's answer affected assessment>
```

### REVIEW_COMPLETE (Agent → Orchestrator)

The standard review output following the agent's Output Format.

### CROSS_DOMAIN_FORWARD (Agent → Orchestrator)

Embedded within findings: `[CROSS-DOMAIN: <target_agent_codename>]`

### UNCERTAINTY_ESCALATION (Agent → Orchestrator)

Embedded within findings: `[UNCERTAIN: <specific question>]`

---

## Rules

1. All communication is technical. No conversational filler.
2. Orchestrator passes information verbatim. No summarization.
3. Agents must not reference other agents' work.
4. Cross-domain items are evaluated independently.
5. Uncertainty questions must be specific and answerable.
6. All findings must cite evidence. No evidence = not a finding.
