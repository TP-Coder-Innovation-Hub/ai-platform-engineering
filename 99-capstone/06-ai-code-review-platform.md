# Capstone: AI Code Review Platform

Build a code-review service that identifies high-confidence correctness, security, and operability risks without overwhelming developers with stylistic noise.

## System scope

- repository and pull-request ingestion;
- changed-file, dependency, and ownership context;
- static-analysis and policy tools;
- model-generated findings with exact file and line references;
- severity, confidence, deduplication, and suppression;
- developer feedback and accepted-risk workflow; and
- evaluation against labeled historical changes.

## Required evidence

Measure precision by severity, recall on known defects, duplicate rate, developer dismissal, latency, and cost. Compare model-only review with a pipeline that uses deterministic analyzers before model reasoning.

Prevent repository instructions, comments, or test fixtures from changing review policy. Restrict access to the pull request's authorized repository context.

## Failure tests

- a code comment contains prompt injection;
- generated code is mistaken for the changed code;
- the reviewer cites a line outside the diff;
- a large change exceeds context; and
- a secret appears in tool output.

## Completion criteria

The platform passes when actionable precision meets the declared threshold, findings are traceable, and low-confidence output does not block delivery.

