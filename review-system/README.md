# Review System

Automated, structured code review using domain-specific AI review agents coordinated by an orchestrator.

## Structure

```
Web/review-system/
├── orchestrator.md          # Orchestrator instructions and protocol
├── agents/                  # Domain-specific review agents (11)
│   ├── 01-architecture.md
│   ├── 02-react-patterns.md
│   ├── 03-runtime-safety.md
│   ├── 04-dx-api.md
│   ├── 05-bundle-performance.md
│   ├── 06-state-data.md
│   ├── 07-testing.md
│   ├── 08-error-resilience.md
│   ├── 09-maintainability.md
│   ├── 10-migration-impact.md
│   └── 11-ripple-effect.md
├── templates/               # Structured output and protocol templates
│   ├── output.md           # Final review output format
│   └── protocol.md         # Agent communication protocol
└── README.md               # This file
```

## Quick Start

### Manual trigger

Tell Copilot:
```
Run the review system against <target_branch>. 
Follow Web/review-system/orchestrator.md for the protocol.
Deploy all agents from Web/review-system/agents/.
Use Web/review-system/templates/ for output format and communication protocol.
```

### Scoped review

```
Run the review system against develop/17, scoped to Web/packages/libs/mae-app-engine-api/.
```

### Subset of agents

```
Run agents arch, react-patterns, runtime-safety against develop/17.
```

## Agents

| # | Codename | Domain | Focus |
|---|----------|--------|-------|
| 01 | `arch` | Architecture & Layering | Dependency direction, boundaries, abstraction |
| 02 | `react-patterns` | React Patterns & Lifecycle | Hooks rules, lifecycle, concurrent mode |
| 03 | `runtime-safety` | Runtime Safety & Isolation | Script execution, resources, races, CSP |
| 04 | `dx-api` | DX & API Design | Types, consistency, discoverability, docs |
| 05 | `bundle-performance` | Bundle Size & Performance | Sizes, tree-shaking, code splitting, runtime perf |
| 06 | `state-data` | State & Data Flow | Ownership, sync, closures, contexts, mutations |
| 07 | `testing` | Testing & Testability | Coverage, testability, strategy |
| 08 | `error-resilience` | Error Handling & Resilience | Boundaries, degradation, recovery, messages |
| 09 | `maintainability` | Maintainability & Code Quality | Complexity, duplication, naming, extensibility |
| 10 | `migration-impact` | Migration & Cross-File Impact | Moves, bridges, defaults, consumers |
| 11 | `ripple-effect` | Ripple Effect & Remote Impact | Blast radius, transitive consumers, implicit contracts |

## Special Agent: Ripple Effect

Agent `11-ripple-effect` works differently from others. While other agents review the code IN the diff, this agent traces the **blast radius** — code NOT in the diff that could break because of the diff. It:
- Identifies changed public exports
- Greps the entire monorepo for consumers of those exports
- Assesses whether untouched consumers still work correctly
- Traces transitive dependency chains

This agent requires extra context from the orchestrator (workspace package list, dependency graph).

## Design Principles

1. **Agent isolation**: Agents cannot see each other's output. They operate independently.
2. **Orchestrator neutrality**: The orchestrator routes information without influencing findings.
3. **Structured I/O**: All communication follows defined protocols with mandatory fields.
4. **Applicability-gated checklists**: Agents first decide which checklist sections apply, then complete only the checks whose preconditions are met. Skipped checks require evidence-backed N/A rationale.
5. **Cross-domain routing**: Out-of-scope findings are routed with structured metadata, evaluated once by the target agent, and tracked through a visible lifecycle.
6. **Severity-fit-for-purpose**: Engineering issues use technical severity; user-facing issues also use UX severity inputs: frequency, user impact, persistence, affected segment, and workaround.
7. **Managed uncertainty**: Blocking ambiguity is escalated; non-blocking ambiguity is documented with assumptions, confidence, and fallback behavior.
8. **Evidence-based**: Every finding must cite file:line evidence. No evidence = not a finding.

## Severity Levels

### Technical Severity

Use technical severity for correctness, reliability, security, architecture, performance, migration, and API-contract issues.

| Level | Criteria |
|-------|----------|
| CRITICAL | Will cause runtime failure, data corruption, or security vulnerability in production |
| HIGH | Significant bug, performance issue, or design flaw that will affect users |
| MEDIUM | Correctness issue or bad practice that should be fixed but won't crash production |
| LOW | Minor issue with limited impact. Should fix when touching the area |
| NIT | Style, naming, or preference. Fix at discretion |

### UX Severity Inputs

For user-facing findings, agents must supplement technical severity with UX impact factors before assigning the final level. A technically small change can be HIGH or CRITICAL when it blocks frequent or important user tasks.

| Factor | Required assessment |
|--------|---------------------|
| Frequency | How often users are expected to encounter the issue |
| Task criticality | Whether the issue affects a primary, revenue-critical, destructive, or compliance-sensitive flow |
| Impact | Whether users are blocked, slowed, misled, likely to make mistakes, or excluded |
| Persistence | Whether the issue happens once, repeats every session, or permanently blocks progress |
| Affected segment | Which users are affected, including keyboard-only and assistive-technology users when applicable |
| Workaround | Whether a reasonable workaround exists without developer/support intervention |

| UX Level | Criteria |
|----------|----------|
| UX-BLOCKER | Prevents completion of a critical task, risks irreversible user harm/data loss, or excludes an affected user group from the flow |
| UX-HIGH | Likely causes task failure, major confusion, repeated errors, or high-cost workaround in an important flow |
| UX-MEDIUM | Slows task completion, increases cognitive load, or creates avoidable errors with a discoverable workaround |
| UX-LOW | Minor friction, inconsistent affordance/copy, or low-frequency confusion that does not block completion |
| UX-NIT | Polish issue with negligible task impact |

## Cross-Domain Routing

When an agent finds something outside its domain:
1. Agent tags the finding and includes routing metadata: source ID, target agent, reason, and requested decision.
2. Orchestrator extracts and forwards the finding with full context to the target agent.
3. Target agent evaluates independently and returns `CONFIRM`, `ADJUST`, or `DISMISS`.
4. Orchestrator records the item lifecycle in the final routed-items table.
5. Routing is limited to one follow-up pass unless the user explicitly approves another pass.
6. Dismissed routed items remain visible in the routing log so users can audit why they disappeared from findings.

## Uncertainty Handling

When an agent is uncertain about scope, intent, or severity:
1. Agent tags the finding: `[UNCERTAIN: <specific question>]`.
2. Agent adds uncertainty metadata: `blocking`, `default_assumption_if_unanswered`, `impact_if_wrong`, and `confidence_without_answer`.
3. Orchestrator escalates only blocking questions to the user via `askUser`.
4. Non-blocking uncertainty remains in the report with the stated assumption and confidence.
5. User answers are passed back to the originating agent verbatim.
6. Agent updates finding based on clarification.

## Output

Final review is written to `Web/review-output-<branch>-<YYYY-MM-DD>.md` following `templates/output.md`.
