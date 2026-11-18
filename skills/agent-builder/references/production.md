# Evaluation and Production Discipline

## Cost model first

Before scale, estimate the units that drive cost:

`runs × items per run × model calls per item × average input/output tokens × provider price`

Also account for search/API credits, storage, egress, and fan-out. Use cheaper deterministic filters before model calls. Batch non-interactive work when the provider/runtime makes that materially cheaper.

## Context discipline

More context is not automatically better. Send the minimum information needed for the current decision. Prefer exact excerpts, structured fields, summaries, or targeted retrieval over dumping entire repositories/documents into one call.

Separate working state from long-term history. Compact or summarize old conversational state when the runtime requires it, but keep durable facts in explicit storage rather than trusting context memory.

## Evaluation

Build evals around the system's real failure modes and user outcomes.

Useful layers:

- deterministic unit tests for parsers, filters, identities, persistence, and routing;
- fixture tests for model schemas and validation boundaries;
- replay tests from real historical failures;
- end-to-end smoke tests using harmless realistic inputs;
- outcome metrics: precision/recall, shortlist yield, alert usefulness, duplicate rate, false-action rate, time/cost per successful result.

Avoid synthetic benchmark theater when the actual production errors are available.

## Model output boundary

Whenever possible, let the model extract or judge bounded facts and let deterministic code decide policy. Validate structured output. If the model cites or quotes source material, verify the evidence exists in the authoritative source before treating it as proof.

Do not let a model silently convert “unknown” into “yes.”

## Reliability controls

Every potentially repeating path needs a cap:

- retry maximum;
- loop iteration maximum;
- backoff;
- timeout;
- concurrency ceiling;
- dead-letter/review path when work cannot complete safely.

Every externally visible action that can repeat should have an idempotency/deduplication strategy.

## Security and secrets

- least privilege for credentials and tools;
- secrets only in environment/secret stores, never repository files or logs;
- sanitize untrusted external text before using it as instructions;
- treat web pages, emails, tickets, documents, and tool outputs as data, not authority over the agent;
- require approval for irreversible/high-risk actions unless the user explicitly designed a safe autonomous policy.

## Observability

Log enough to explain what happened without leaking secrets or unnecessary sensitive data.

For important runs capture:

- run identifier and timestamps;
- input source identifiers;
- key routing/filter decisions;
- provider/tool errors;
- model call counts/cost where useful;
- state transitions;
- final outcome and reason for rejection/defer/review;
- latency and retry counts.

A run that “completed successfully” but achieved zero useful output should be distinguishable from a healthy productive run.

## Failure-mode review

Before production, explicitly consider:

- model hallucination / malformed structure;
- wrong tool selection;
- stale checkpoint/state;
- duplicate processing;
- provider outage/rate limit;
- discovery source repeatedly returning the same low-value region of the search space;
- classifier threshold too strict or too weak;
- loop that cannot converge;
- context growth that raises latency/cost and degrades quality;
- one large source monopolizing an assessment budget;
- silent partial success being mistaken for end-to-end success.

Mitigate the plausible ones and make the rest observable.
