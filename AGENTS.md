# AGENTS.md

## Context

This repository contains the AI Platform Engineering learning path. It is for software, DevOps, data, and ML engineers moving into production AI platform work.

## Content rules

- Organize material by chapters and capabilities. Never use weeks, days, or calendar pacing.
- Write original explanations. Do not mirror another handbook's wording or section order.
- Lead with architecture, contracts, and failure modes. Tools are examples, not the curriculum.
- Connect every chapter to a system outcome and a checkpoint.
- Prefer runnable, focused examples over large pasted configurations.
- Use Mermaid for architecture, sequence, and lifecycle diagrams. Add a one-sentence text summary before each diagram.
- Distinguish deterministic workflows from model-driven behavior.
- Treat evaluation, security, cost, and observability as design inputs.
- Do not claim that a system is production-ready without rollback, ownership, and incident handling.
- Avoid hype, emojis, career-branding filler, and vendor scorecards.

## Audience

- Engineers comfortable with Git, APIs, Linux, and one programming language
- Teams designing shared AI capabilities rather than one-off demos
- Learners who need practical production judgment

## Chapter shape

Each chapter should contain:

1. the engineering problem;
2. the system model;
3. design decisions and trade-offs;
4. failure modes;
5. a practical checkpoint; and
6. clear completion criteria.

## Technical stance

- Build once and promote immutable artifacts.
- Version code, data, models, prompts, evaluation sets, and configuration.
- Default to least privilege and short-lived identity.
- Use structured contracts at every model boundary.
- Start with a workflow. Add agent autonomy only when runtime judgment is required.
- Evaluate before deployment and observe after deployment.
- Optimize cost per successful task, not cost per token in isolation.

