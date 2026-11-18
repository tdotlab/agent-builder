# Evolution Playbook

## Extension vs redesign test

An extension should be possible behind existing boundaries. If adding the feature requires changing several unrelated components, replacing the state model, or emulating unsupported runtime behavior, stop calling it an extension.

Examples:

- add Discord alongside Telegram through a notifier interface → extension;
- add durable human pause/resume to a runtime with no durable checkpoints → likely migration/redesign;
- add one source to an existing normalized provider interface → extension;
- replace ad-hoc in-memory state with durable multi-run state because the product depends on it → architectural change.

## Cost optimization

Quantify where spend originates before changing prompts. Track model calls per successful outcome, tokens per call, search/API credits, retries, and repeated work.

The highest leverage often comes from not asking the model a question at all.

## Latency optimization

Draw the critical path. Parallelize only independent stages. Cache expensive deterministic/static data. Stream when it improves perceived latency without hiding unfinished work.

Avoid parallel fan-out that overwhelms downstream rate limits; concurrency caps are part of correctness.

## Prompt/model optimization

Decompose multi-criteria judgments when one prompt produces inconsistent answers. Use structured output. Evaluate against real examples. Route easy cases away from expensive models.

Prompt tuning cannot repair missing evidence, wrong state, or bad retrieval.

## Retrieval evolution

Before adding RAG complexity, identify the failing query shape. Improve sparse/dense/hybrid retrieval, metadata filtering, chunking, reranking, or query decomposition as justified by measured misses.

## Migration strategy

Preserve contracts at the edges:

- input schema;
- output schema;
- persistence semantics;
- identity/dedup rules;
- external side effects;
- user-visible failure behavior.

Use adapters so old and new implementations can be compared during transition.
