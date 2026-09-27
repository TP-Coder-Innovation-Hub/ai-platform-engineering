# Chapter 14: Distributed AI Infrastructure

AI infrastructure converts expensive accelerators, memory, networks, and storage into predictable training and inference capacity. Utilization alone is not the goal; useful work within service objectives is.

## GPU execution model

GPUs provide parallel compute and high-bandwidth device memory. Model weights, activations, and key-value cache compete for memory. Tensor cores accelerate supported matrix operations, while the driver, runtime, communication libraries, and framework must remain compatible.

Profile before optimizing. A service can be compute-bound, memory-capacity-bound, memory-bandwidth-bound, network-bound, or queue-bound. Adding GPUs to the wrong bottleneck increases cost.

## Accelerator software stack

The kernel driver controls the device. The compute runtime exposes programming and execution APIs. Optimized libraries provide neural-network kernels and collective communication. Framework builds support specific combinations of runtime and driver versions.

Record the complete compatibility matrix in the runtime image and node configuration. A container does not include the host driver. Validate devices at node admission and after upgrades.

GPU memory holds weights, activations, temporary workspaces, and inference cache. Out-of-memory errors can arise from peak allocation or fragmentation. Measure real sequence distributions and leave operating headroom.

## Workload economics

Track useful tokens, examples, or completed tasks per accelerator hour. High utilization can still produce poor economics if requests queue too long or batches contain wasteful padding. Idle headroom may be necessary for latency objectives.

Separate interactive, batch, and training pools when their scheduling goals conflict. Use quotas and priorities to prevent experiments from displacing production.

## Serving mechanics

Continuous batching combines requests as decoding progresses. The key-value cache avoids recomputing attention state but grows with sequence length and concurrency. Quantization reduces memory and may improve throughput, with a possible quality cost. Speculative decoding uses a smaller model to propose tokens that a larger model verifies.

Capacity planning connects these mechanisms:

```text
required replicas = peak arrival rate × target service time / safe concurrency
```

This is a starting approximation. Validate it with representative prompt lengths, output lengths, streaming behavior, and failure conditions.

Continuous batching admits new requests between decoding steps, improving utilization under mixed arrival times. Paged cache management reduces memory waste. Prefix caching helps repeated trusted prefixes but needs correct isolation and invalidation.

Quantization reduces weight memory and bandwidth. Evaluate task quality, supported hardware, kernel availability, and throughput; theoretical compression does not guarantee faster serving. Speculative decoding helps only when the draft model is fast and its proposals are accepted often enough.

Time to first token and inter-token latency describe different user experiences. Report both with end-to-end request latency and queue time.

## Distributed systems

Training may parallelize data, model parameters, tensors, or pipeline stages. Communication overhead and checkpoint recovery determine whether more devices help. Inference may shard a model across devices or replicate it for throughput.

On Kubernetes, use accelerator-aware scheduling, node pools, taints, priorities, quotas, and topology constraints. Scale on queue depth or waiting time when CPU metrics do not represent demand. Account for model download and warm-up before marking a replica ready.

### Distributed training

Data parallelism replicates the model and splits batches. Tensor parallelism shards operations across devices. Pipeline parallelism splits layers into stages. Fully sharded approaches distribute parameters, gradients, and optimizer state.

Communication can dominate scaling. Devices within one node may have faster links than devices across nodes. Place workers topology-aware and benchmark scaling efficiency. More workers can increase failure probability and checkpoint overhead.

Checkpoint model, optimizer, scheduler, random state, and progress. Write atomically or through versioned directories. Test resuming with the supported topology and software version.

### Distributed inference

Replicate a model for throughput when it fits one device. Shard it when memory or latency requires several devices. A sharded replica fails as a unit and depends on fast communication. Load balancing should account for queue and cache state, not only replica count.

## Kubernetes AI operators

Device plugins advertise accelerator resources. GPU operators can manage drivers, plugins, monitoring, and supporting components. Pin and test upgrades because a node-stack change affects every workload.

Model-serving controllers can manage revisions, traffic, autoscaling, and protocol adapters. Training operators can represent distributed worker roles. These abstractions help only when teams understand the generated workloads and failure states.

Node provisioning must include image pull, model download, storage bandwidth, driver readiness, and topology. Autoscaling a node pool is slower than adding a warm pod; capacity plans need both layers.

## Serving engines and abstraction

Choose an engine based on supported architectures, batching, quantization, streaming, observability, hardware, and operational maturity. Keep a stable inference contract, but do not hide features required for performance or correctness behind the lowest common denominator.

Benchmark engines with the exact model, quantization, hardware, prompt distribution, output distribution, concurrency, and streaming mode. Capture quality as well as throughput. A faster engine that changes tokenization or numerical behavior can affect evaluation.

## Infrastructure monitoring

Collect request queue, batch size, cache usage, time to first token, token throughput, accelerator utilization, memory, power, temperature, throttling, interconnect traffic, and errors. Correlate these with model and engine revision.

Use profiles to inspect kernel and CPU gaps. Host preprocessing, tokenization, network transfer, or logging can starve the GPU. Monitor storage and registry throughput during rollout because cold model loading can be the dominant recovery time.

## Failure and recovery

Plan for device failure, node loss, collective timeout, checkpoint corruption, registry outage, and regional capacity shortage. Define retry boundaries so a failed distributed job does not restart forever.

For serving, shed load before queues become unrecoverable. Maintain an approved smaller model or asynchronous fallback where product requirements permit it. Test cold recovery without relying on a warm cache.

## Failure modes

- autoscaling adds cold replicas slower than the queue grows
- memory fragmentation causes out-of-memory failures under mixed sequence lengths
- one noisy tenant consumes the cache and queue
- distributed training checkpoints cannot resume on different topology
- benchmark traffic does not match production inputs

## Checkpoint

Create a capacity plan for a streaming model service. Include latency targets, prompt and output distributions, cache memory, safe concurrency, warm-up, scale signals, quota, and cost.

Completion means the plan explains saturation behavior and degraded operation, not only peak throughput.
