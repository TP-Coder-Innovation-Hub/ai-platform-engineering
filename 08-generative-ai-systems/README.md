# Chapter 08: Generative AI Systems

A language model predicts tokens from context. The application around it determines access, structure, safety, latency, and usefulness.

## Runtime model

Text is tokenized, represented as vectors, processed through transformer layers, and decoded one token at a time. The context window is finite working space, not durable memory. Temperature and sampling settings change output distribution; they do not repair weak evidence or unclear instructions.

Hallucination is a system property to manage, not a switch to disable. Reduce unsupported output with grounded context, constrained tasks, structured responses, verification, and abstention.

## Transformer execution model

Tokenization maps text to model-specific identifiers. The same sentence can use different token counts across models, which changes cost and context use. Inputs are converted to embeddings and combined with positional information so attention can relate tokens across the sequence.

Self-attention computes relationships among tokens. Transformer layers combine attention and feed-forward computation to produce a probability distribution for the next token. Decoding samples or selects a token, appends it, and repeats.

The model does not retrieve a stored paragraph from its weights. It generates from learned statistical structure and the supplied context. This explains why fluent output can be unsupported.

Temperature reshapes the distribution. Top-k limits candidates to a fixed number; top-p limits them to a cumulative probability mass. Lower randomness can improve repeatability but does not guarantee truth or identical output.

## Context engineering

Context includes system instructions, conversation state, retrieved evidence, tool descriptions, tool results, examples, and user input. Every item competes for a finite budget and can influence behavior.

Prioritize authoritative instructions and relevant evidence. Remove duplicated history and stale tool output. Summaries reduce size but are lossy derived data. Preserve source records when later verification matters.

Long context does not remove retrieval design. More tokens increase latency and cost and can reduce attention to critical evidence. Measure quality across realistic context sizes.

## Model access layer

Put provider SDKs behind a typed gateway contract. Record model revision, parameters, token use, latency, request class, and policy result. Support timeouts, cancellation, rate limits, and streaming without exposing provider-specific objects to product code.

Route by task requirements:

- quality and reasoning depth;
- context size and modality;
- latency target;
- data location and provider policy;
- tool or structured-output support; and
- cost ceiling.

Evaluate routing on real tasks. A cheaper model that causes more retries or human corrections can cost more per successful outcome.

## Managed and self-hosted models

Managed APIs reduce infrastructure work and provide rapid access to capable models. They introduce provider quotas, policy, data-processing terms, regional availability, and model lifecycle dependencies.

Self-hosting provides control over weights, runtime, locality, and capacity. It introduces accelerator procurement, serving optimization, security patching, scaling, and on-call ownership. Open weights do not automatically grant unrestricted licensing or safe use.

Use a decision record covering quality, latency, throughput, context, modality, data policy, availability, cost, and operational capability. Avoid choosing based on a single public benchmark.

## Streaming and rate limits

Streaming reduces time to first token but not total generation time. The application must handle partial UTF-8 content, disconnects, provider errors after output starts, and validation that may finish only at the end.

Rate limits can apply to requests, input tokens, output tokens, or concurrent work. Centralize quota accounting and apply backpressure. Independent service retries can multiply traffic during a provider incident.

## Prompt systems

Treat prompts as versioned program inputs. Separate trusted instructions from untrusted user content and retrieved data. Define the expected output schema and validate it in code. Few-shot examples should represent difficult boundary cases, not only ideal outputs.

A prompt template should define variables and escaping rules. Validate required inputs before rendering. Record the template revision, model, parameters, tool set, and output schema in evaluation and production traces.

Use zero-shot instructions for simple, well-defined tasks. Add examples when format or decision boundaries need demonstration. Use decomposition when subtasks can be verified independently. Use tool calls for current data and deterministic actions.

Do not request verbose hidden reasoning as proof. Ask for observable evidence: selected category, cited source, calculation result, tool trace, or concise rationale permitted by the product.

## Structured responses and tools

Schema-constrained generation narrows syntax, not truth. Validate types, ranges, identifiers, authorization, and cross-field rules after generation. For safety-critical actions, treat model output as a proposal evaluated by deterministic policy.

Tool descriptions should be precise and non-overlapping. Return bounded structured results and explicit errors. Tool output is untrusted content even when the tool itself is trusted, because it may contain external text.

## Evaluation and model selection

Build a task dataset with normal, boundary, adversarial, and unanswerable cases. Measure correctness, instruction following, schema validity, safety, latency, and cost. Evaluate important user and language slices.

Compare models under the same prompt and then allow model-specific optimization as a second experiment. Otherwise a prompt tuned for one model can create an unfair comparison.

Pin the evaluated revision where the provider supports it. When only moving aliases exist, run continuous regression tests and maintain a tested fallback.

Reasoning techniques are task-dependent. Do not require hidden reasoning text or expose it as evidence. Ask for concise answers, structured decisions, citations, or verifiable intermediate artifacts instead.

## Failure modes

- model aliases change behavior without evaluation
- retries consume quota after deterministic validation errors
- streaming output bypasses safety or schema validation
- the system prompt contains secrets
- prompt injection is treated as a prompt-writing problem only

## Checkpoint

Design a provider-neutral model gateway with a typed request, structured response, routing policy, quota, telemetry, and fallback rules. Create an evaluation set before comparing models.

Completion means model choice is an evidence-based deployment decision, not a hard-coded SDK call.
