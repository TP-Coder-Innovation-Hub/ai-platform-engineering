# Capstone: AI Workflow Automation Platform

Build a platform that combines deterministic workflows with bounded model decisions for document-driven business processes.

## System scope

- versioned workflow definitions and durable execution;
- document ingestion and structured extraction;
- model-based classification behind schemas;
- deterministic routing, policy, and state transitions;
- human queues for ambiguity and high-impact decisions;
- idempotent integrations with two enterprise systems; and
- tenant quotas, audit, observability, and rollback.

## Required evidence

Implement one process such as warranty claims, invoice exceptions, or access requests. Measure straight-through completion, correction, human review load, cycle time, error severity, and cost.

Compare a rules-only baseline, an unconstrained agent, and the hybrid design. State why each model decision cannot be replaced by a simpler rule.

## Failure tests

- duplicate events arrive out of order;
- extraction returns valid syntax with inconsistent values;
- an integration succeeds but its acknowledgement is lost;
- policy changes while work is in progress; and
- a reviewer rejects the model's proposed classification.

## Completion criteria

The platform passes when workflow state is recoverable, actions are idempotent, ambiguity reaches the right reviewer, and model output never controls authorization directly.

