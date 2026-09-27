# Capstone: AI Security Testing Platform

Build a repeatable security evaluation platform for RAG and tool-using applications.

## System scope

- versioned attack suites for direct and indirect prompt injection;
- tests for data leakage, excessive agency, cross-tenant access, and denial of service;
- isolated test identities, synthetic data, and disposable environments;
- deterministic policy assertions plus model-assisted grading;
- finding severity, evidence, ownership, and regression tracking; and
- release gating for critical failures.

## Required evidence

Threat-model one application and map every critical risk to a test and runtime control. Calibrate automated grading against human review. Report false positives and false negatives.

Demonstrate that a failed critical test blocks promotion and that a fixed finding remains in the regression suite.

## Failure tests

- an attack payload appears inside a retrieved document;
- a model judge approves an unsafe action;
- the test runner receives production credentials;
- an external target escapes the allowlist; and
- a finding contains sensitive test data.

## Completion criteria

The platform passes when tests are reproducible, isolated, tied to owned controls, and produce release evidence rather than an unstructured vulnerability report.

