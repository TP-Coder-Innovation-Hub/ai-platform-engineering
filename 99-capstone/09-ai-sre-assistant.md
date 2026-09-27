# Capstone: AI SRE Assistant

Build a reliability assistant that explains service health, evaluates error-budget burn, and proposes capacity or remediation work without changing production directly.

## System scope

- service catalog, SLO, deployment, and ownership context;
- metrics, logs, traces, incidents, and change-event tools;
- burn-rate and capacity analysis through deterministic calculations;
- model-generated summaries and prioritized hypotheses;
- review workflow for recommendations; and
- privacy-safe operational memory and audit.

```mermaid
flowchart LR
    C[Service catalog, SLOs, and ownership] --> E[Evidence collector]
    T[Metrics, logs, traces, incidents, and changes] --> E
    E --> D[Deterministic burn-rate and capacity calculations]
    E --> H[Evidence-linked hypotheses]
    D --> A[Reliability assistant]
    H --> A
    A --> R[Prioritized recommendation with cost context]
    R --> V[Operator review]
    V -->|accepted| B[Owned backlog or change process]
    V -->|rejected| F[Feedback and evaluation case]
    B --> X[Existing production change control]
```

## Required evidence

Evaluate alert interpretation, calculation correctness, evidence coverage, unsupported conclusions, prioritization, latency, and operator acceptance. Compare the assistant with existing dashboards and runbooks.

## Failure tests

- an SLO definition is missing or stale;
- telemetry sources disagree;
- a dashboard label contains malicious text;
- the assistant recommends capacity without cost context; and
- a user requests action outside the assistant's read-only scope.

## Completion criteria

The assistant passes when every conclusion links to current operational evidence, deterministic calculations remain reproducible, and recommendations cannot bypass change control.
