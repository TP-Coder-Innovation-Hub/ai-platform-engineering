# Capstone: Documentation Intelligence Platform

Build a platform that detects stale technical documentation, drafts evidence-backed updates, and routes changes through normal repository review.

## System scope

- repository, API schema, deployment, and service-catalog ingestion;
- document-to-system ownership mapping;
- change detection and stale-content scoring;
- cited update drafts with exact source evidence;
- pull-request creation behind human approval; and
- feedback, suppression, and audit history.

```mermaid
flowchart LR
    R[Repositories and API schemas] --> C[Versioned evidence index]
    D[Deployments and service catalog] --> C
    C --> X[Change detector]
    X --> O[Ownership and document mapping]
    O --> S[Staleness score]
    S --> G[Cited update draft]
    G --> V[Evidence and protected-path validation]
    V --> H{Owner approval}
    H -- approved --> P[Reviewable pull request]
    H -- rejected --> F[Feedback or suppression record]
    P --> A[Repository audit history]
```

## Required evidence

Create a labeled set of current and stale documents. Measure detection precision, missed changes, unsupported edits, reviewer acceptance, and time saved. Require every proposed change to cite the code, schema, or configuration that supports it.

## Failure tests

- generated code is mistaken for source code;
- two authoritative sources conflict;
- repository text attempts prompt injection;
- ownership metadata is missing; and
- a draft targets a protected document without approval.

## Completion criteria

The platform passes when it improves documentation through reviewable drafts, preserves ownership, and never publishes model output directly.
