# Review System

Automated, structured code review using domain-specific AI review agents coordinated by an orchestrator.

## Structure

```
Web/review-system/
├── orchestrator.md          # Orchestrator instructions and protocol
├── agents/                  # Domain-specific review agents (10)
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
4. **Mandatory checklists**: Agents must complete all checkboxes or justify N/A.
5. **Cross-domain routing**: Out-of-scope findings are tagged and routed to the correct agent.
6. **Zero uncertainty**: All ambiguity must be escalated and resolved. No assumptions.
7. **Evidence-based**: Every finding must cite file:line evidence. No evidence = not a finding.

## Severity Levels

| Level | Criteria |
|-------|----------|
| CRITICAL | Will cause runtime failure, data corruption, or security vulnerability in production |
| HIGH | Significant bug, performance issue, or design flaw that will affect users |
| MEDIUM | Correctness issue or bad practice that should be fixed but won't crash production |
| LOW | Minor issue with limited impact. Should fix when touching the area |
| NIT | Style, naming, or preference. Fix at discretion |

## Cross-Domain Routing

When an agent finds something outside its domain:
1. Agent tags the finding: `[CROSS-DOMAIN: <target_agent_codename>]`
2. Orchestrator extracts and forwards to the target agent
3. Target agent evaluates independently (CONFIRM/ADJUST/DISMISS)
4. Orchestrator keeps the higher-confidence assessment in final output

## Uncertainty Handling

When an agent is uncertain about scope, intent, or severity:
1. Agent tags: `[UNCERTAIN: <specific question>]`
2. Orchestrator escalates to user via `askUser`
3. User's answer is passed back to agent verbatim
4. Agent updates finding based on clarification

## Output

Final review is written to `Web/review-output-<branch>-<YYYY-MM-DD>.md` following `templates/output.md`.
