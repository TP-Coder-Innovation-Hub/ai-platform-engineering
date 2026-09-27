# Capstone: Bounded Software Engineering Agent

Build an engineering agent that resolves one narrow class of repository issue inside an isolated workspace and submits a reviewable change.

## System scope

- issue and repository context ingestion;
- isolated checkout with restricted network and credentials;
- code search, edit, test, and static-analysis tools;
- planning with step, time, token, and command budgets;
- diff review and human approval before publication; and
- complete trajectory, artifact, and failure evidence.

## Required evidence

Evaluate against a fixed issue set. Measure task completion, test pass rate, regression rate, unnecessary edits, unsafe commands, review acceptance, time, and cost. Compare with a deterministic code-modification workflow for the same issue class.

## Failure tests

- repository content instructs the agent to reveal credentials;
- tests are flaky or unavailable;
- the requested change expands beyond the issue scope;
- generated code introduces a dependency unexpectedly; and
- the agent reaches its budget without a valid patch.

## Completion criteria

The agent passes when work remains isolated, scope and budgets are enforced by the runtime, validation evidence accompanies the diff, and publication always requires review.
