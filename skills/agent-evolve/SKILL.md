---
name: agent-evolve
description: Use when an existing working agent or automation needs to be extended, migrated, optimized, hardened, or given new capabilities such as memory, HITL, retrieval, tools, scheduling, observability, or lower cost/latency.
---

# Agent Evolve

Change a working agentic system without casually destroying the behavior that already works.

Use `agent-repair` instead when the current system is fundamentally broken or failing its core outcome. Use `agent-builder` for greenfield work.

## 1. Map the current contract

Before changing architecture, identify:

- current trigger and user-visible outputs;
- state schema and durable identities;
- framework/runtime and deployment;
- model/provider boundary;
- tool/integration contracts;
- existing tests and production invariants.

The extension must preserve unrelated behavior unless the user explicitly authorizes a redesign.

## 2. Classify the change

### Extension

A new capability fits the existing architecture without changing its core execution model. Examples: add a tool, new notification channel, memory field, scheduled trigger, approval gate, metrics, or an additional deterministic filter.

### Optimization

Same product behavior, lower cost/latency or better reliability/quality. Examples: model routing, caching, context reduction, parallelizing independent stages, batch processing, prompt decomposition, stricter deduplication.

### Migration

The requested capability conflicts with the existing framework/runtime/provider or the user deliberately wants to move stacks. Preserve external behavior and migrate behind stable interfaces.

### Redesign

Core topology or state model must change. At that point treat the work like a scoped architecture project; do not pretend it is a small extension.

Read `references/evolution.md` when the distinction is unclear.

## 3. Prefer compatible additions

Locate the narrowest extension point. Add new state fields rather than repurposing old fields. Add a node/stage/tool behind an existing interface rather than rewriting neighboring components.

Before adding infrastructure, check whether the host/platform already provides the needed capability.

## 4. Optimize in high-ROI order

When the goal is cost/performance, check these before prompt micro-tuning:

1. remove unnecessary model calls;
2. move deterministic filters earlier;
3. route simple work to cheaper models or deterministic code;
4. reduce context and tool output size;
5. deduplicate/cache repeated work;
6. parallelize truly independent work;
7. cap loops/fan-out;
8. batch non-interactive workloads;
9. tune prompts/evals after the system boundary is sane.

Measure before and after when runtime data is available.

## 5. Migrate by contract

For framework/provider migration:

- document current observable behavior;
- isolate provider/framework-specific adapters;
- create regression fixtures;
- move one boundary at a time;
- compare outputs/state transitions;
- keep rollback possible until the new path is verified.

Do not migrate simply because a newer framework is fashionable.

## 6. Harden the new surface

Every added capability introduces new failure modes. Add the corresponding controls:

- new loop → iteration cap;
- new external call → timeout/retry/rate-limit behavior;
- new write action → idempotency and approval boundary where appropriate;
- new memory → lifecycle/retention/identity rules;
- new retrieval → relevance/faithfulness evaluation;
- new scheduler → duplicate-run/concurrency protection;
- new model → cost and fallback policy;
- new provider → provider-error observability.

## 7. Regression verification

Test the new behavior and the old contract.

When possible run:

- targeted tests for the extension;
- existing full suite;
- a harmless end-to-end sample;
- before/after metric comparison for optimizations;
- migration parity fixtures for provider/framework changes.

A faster system that changes important outputs is not an optimization unless the user accepts the tradeoff.

## Completion

Summarize the extension/migration/optimization, what existing behavior was protected, verification evidence, and any new operational requirements.
