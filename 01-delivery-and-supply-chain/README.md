# Chapter 01: Delivery and the Software Supply Chain

Production AI starts with ordinary software discipline. If code, dependencies, and artifacts cannot be reproduced, model lineage will not save the system.

## From commit to evidence

A delivery pipeline should turn a reviewed commit into one immutable artifact and a chain of evidence.

```mermaid
flowchart LR
    C[Signed commit] --> T[Test and scan]
    T --> B[Build once]
    B --> S[Generate SBOM and provenance]
    S --> R[Artifact registry]
    R --> D[Deploy by digest]
    D --> V[Verify and observe]
    V -->|Failed gate| X[Rollback]
```

The same artifact moves through environments. Rebuilding for production changes the subject under test and breaks provenance.

## Git as the system of record

Git stores content-addressed objects. Blobs contain file content, trees describe directories, commits point to a tree and parent commits, and references give human-readable names to commits. This matters during recovery: a branch is a movable reference, while a commit identifier names an immutable snapshot.

Use history operations according to their risk:

- `revert` adds a new commit that reverses an earlier change and is safe for shared history;
- `reset` moves a local reference and can discard reachable work if used carelessly;
- `rebase` recreates commits on a new base and should not rewrite history other people consume;
- `cherry-pick` copies a specific change and can create duplicate history; and
- `bisect` searches commit history to identify the first change that introduced a reproducible failure.

The platform should make recovery routine. Protect important branches, retain build evidence, back up critical repositories, and test restoration. Signed commits identify the signing key, not the correctness of the change. Reviews and automated checks remain necessary.

## Branch and release strategy

Short-lived branches reduce integration risk. Keep changes small enough to review and validate. Long-lived release branches can be justified for supported product versions, but they create backport and security-patch work.

Choose a merge policy deliberately:

| Policy | Strength | Cost |
|---|---|---|
| Merge commit | Preserves branch topology and review context | Noisier history |
| Squash merge | Produces one clean change per review | Loses individual commit boundaries |
| Rebase merge | Produces linear history | Rewrites commit identities |

Semantic versioning communicates compatibility for released interfaces. It does not replace a migration plan. AI systems also need revisions for models, prompts, schemas, and evaluation sets. These revisions may change behavior without changing an HTTP API.

Create a release record that points to immutable identifiers. Human-friendly tags can move or be deleted; artifact digests and commit identifiers provide stronger identity.

## Git and review discipline

Prefer short-lived branches and small changes. Protect the main branch with required checks and reviews. Use merge commits, squash, or rebase according to the repository's audit needs; the important property is that the accepted state is traceable.

Use tags for human release names and digests for deployment identity. A mutable tag such as `latest` is not evidence of what ran.

## Pipeline stages

Order gates from fast and cheap to slow and expensive:

1. format, lint, type, and policy checks;
2. unit and contract tests;
3. dependency and secret scanning;
4. artifact build;
5. integration and evaluation tests;
6. image and infrastructure scanning;
7. signing, provenance, and registry publication; and
8. environment deployment and verification.

Use workload identity for CI. Long-lived cloud keys in repository secrets create avoidable rotation and exfiltration risk. Give each job only the permissions it needs.

## Pipeline architecture

Separate validation, build, publication, and deployment. Pull-request jobs should prove that a proposed change is acceptable but should not normally publish trusted releases. Protected-branch jobs create release artifacts. Environment workflows promote approved artifacts.

Use reusable pipeline components for repeated policy, but keep their versions pinned. A shared pipeline referenced by a moving branch can change every consuming repository without review.

Parallelize independent jobs. Do not parallelize steps that share mutable state or whose order is part of the evidence. Cancel obsolete pull-request runs to save capacity, while preserving release and deployment runs needed for audit.

### Cache versus artifact

A cache accelerates recomputation and may disappear without affecting correctness. An artifact is an output required by later stages or users and needs retention, integrity, and access policy.

Cache keys should include dependency manifests, toolchain versions, platform, and relevant configuration. Restoring a partially matching cache can save time, but the build must remain correct when the cache is empty or stale.

Artifacts should include checksums and metadata. Restrict who can overwrite or delete release artifacts. Prefer immutable registry settings for production repositories.

### Matrix testing

Use a matrix when the product supports several runtimes, architectures, accelerators, or dependency versions. Keep the supported matrix explicit. An unbounded matrix consumes capacity without adding useful confidence.

For AI workloads, distinguish tests that need no model, a small local model, a provider sandbox, or expensive accelerator capacity. Most pull-request checks should run without privileged credentials or scarce hardware.

## AI-specific additions

An AI release may combine several independently versioned inputs:

- application image digest;
- model identifier and revision;
- prompt or agent policy revision;
- retrieval index snapshot;
- evaluation dataset revision; and
- runtime configuration.

Store these in a release manifest. Rollback must restore the complete compatible set, not only the application image.

An example manifest shape:

```yaml
release_id: support-assistant-2026-09-27.1
source_commit: 7f6b2d1
application_image: registry.example.com/support@sha256:abc123
model:
  provider: managed-provider
  identifier: reasoning-model
  revision: "2026-09-15"
prompt_bundle: sha256:def456
retrieval_index: policies-2026-09-26T18-00Z
tool_schema: support-tools-v4
evaluation_set: support-regression-v12
policy_bundle: sha256:789abc
```

The manifest must not contain secrets. It should reference secret versions or identities where that evidence is required.

## Software supply-chain controls

The supply chain includes source, dependencies, build workers, pipeline definitions, base images, model artifacts, plugins, and deployment systems. Protecting only the application repository leaves several paths to production open.

### Dependency control

Commit lockfiles, verify package integrity, scan for known vulnerabilities, and review new maintainers or unexpected ownership changes for critical dependencies. Use an internal proxy or allowlist where risk requires it. Remove unused dependencies because every package adds update and compromise surface.

### Isolated builds

Use ephemeral workers and minimal job permissions. Do not expose production secrets to pull requests, especially contributions from forks. Separate untrusted build steps from signing and publication. Restrict network access when builds should not download arbitrary content.

### SBOM and provenance

A software bill of materials lists components in an artifact. Provenance records how the artifact was produced: source, builder, steps, and inputs. Signing binds an identity to an artifact or attestation.

These controls support investigation and policy. They do not prove the artifact is safe. Verification must occur before deployment, and revocation or incident procedures must exist for compromised keys and builders.

### Model and data supply chain

Treat downloaded models, adapters, tokenizer files, datasets, and serialized objects as untrusted. Verify origin and hash, inspect licenses, scan formats where possible, and avoid loading arbitrary executable serialization in privileged environments.

Record the base model and adapter relationship. A safe application image can still load a replaced model artifact after startup if the model reference is mutable.

## Secrets and identity

Prefer short-lived, federated identity from the CI system to the cloud or registry. Bind identity to repository, branch, workflow, and environment claims. Require environment approval for high-risk production roles.

Masking a value in logs is a last line of defense. Prevent secrets from entering command output, generated artifacts, caches, and test reports. Rotate exposed credentials immediately; deleting the log is not enough.

Separate identities for build, publish, deploy, and operate. A compromised test job should not be able to replace a production artifact or change a cluster.

## Deployment and rollback

Deployment verification checks both runtime health and product behavior. A successful scheduler rollout does not prove that the model, retrieval index, or tool path works.

Use progressive delivery where risk justifies it:

1. deploy the immutable release to a controlled environment;
2. run smoke and contract tests;
3. expose shadow or canary traffic;
4. compare operational and quality signals;
5. pause or continue according to policy; and
6. record the promotion decision.

Rollback must be rehearsed. Database, index, and schema changes need forward-and-backward compatibility or a forward-fix plan. If a model provider no longer serves an old revision, a manifest alone cannot restore it; the dependency strategy must account for that limitation.

## Pipeline observability

Measure queue time, execution time, flaky tests, cache effectiveness, failure reasons, artifact publication, deployment frequency, rollback rate, and time to recovery. Attach pipeline and release identifiers to deployment events and runtime telemetry.

Alert on failures that block delivery or weaken controls. A single failed feature-branch job usually needs feedback to its author, not an operations page.

## Failure modes

- Tests download unpinned models or datasets
- CI and production use different preprocessing code
- Credentials can publish from pull-request jobs
- A model alias changes without a release event
- An SBOM is generated but never checked during incident response

## Checkpoint

Define a release manifest for an inference service. Include immutable identifiers for code, model, data contract, prompt, and configuration. Design a pipeline that rejects unsigned artifacts and can promote the same digest from staging to production.

Completion means a responder can identify and reproduce every component of a running release.
