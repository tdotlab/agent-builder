---
name: agent-repair
description: Use when an existing agent, bot, monitor, automation, or LLM workflow is broken, unreliable, incomplete, over-engineered, producing poor results, or technically running but failing the user's real objective. Includes architecture review tied to diagnosis and repair.
---

# Agent Repair

Make an existing agentic system achieve its intended outcome. Diagnose before redesigning.

If the system already works and the request is primarily a new capability, migration, or optimization, use `agent-evolve`. If there is no existing system yet, use `agent-builder`.

If Superpowers or another process skill is installed, use its systematic debugging/TDD/verification workflow. This skill defines the agent-specific diagnostic model.

## 1. Recover the intended outcome

Do not infer success from the current implementation. Read the available evidence in this order when possible:

1. explicit user goal in the conversation;
2. repository instructions and README/design docs;
3. tests and configuration;
4. recent commits/issues/PRs;
5. deployment workflows and runtime logs;
6. persisted state/database rows or produced artifacts.

Write down the **intended outcome** in one or two concrete sentences and the observable evidence that would prove it works.

## 2. Map what actually exists

Inspect before editing. Identify:

- trigger and deployment path;
- acquisition/discovery sources;
- deterministic filters;
- model calls and what judgment they own;
- state/persistence and deduplication;
- external actions;
- publication/output gate;
- tests and observability.

Distinguish the planned architecture from the code currently deployed.

Use `host-workspace-operator` for repository inspection when host capabilities exist.

## 3. Separate failure classes

A system can be technically healthy and still fail as a product. Classify the bottleneck before changing code.

### Execution failure

Crashes, dependency errors, bad credentials, schema mismatches, broken deployment, timeouts, provider failures.

### State failure

Duplicates, stale data, missing checkpointing, incorrect identity keys, state overwrites, retries that reprocess work, no progression across runs.

### Architecture failure

Wrong abstraction: uncontrolled agent loop where deterministic stages are needed, framework cannot support required durability/HITL, excessive coupling, one component owns incompatible responsibilities.

### Model-boundary failure

Model is asked to make policy decisions that should be deterministic, model output is unvalidated, context is noisy, prompt combines too many judgments, or unknown evidence is treated as confirmation.

### Upstream/discovery/data failure

The pipeline runs and filters correctly but the acquisition source repeatedly yields irrelevant, duplicated, geographically restricted, stale, or otherwise low-value candidates. More model calls will not fix a bad candidate pool.

### Product-rule failure

The implementation optimizes the wrong metric or applies a gate that does not match the user's actual requirement.

Read `references/diagnostics.md` for symptom-to-investigation guidance.

## 4. Find the root cause, not the first ugly thing

Use evidence from tests/logs/state/live output. Reproduce a concrete failure when possible.

For each proposed change be able to answer:

- what observed failure does this explain?
- why does this change address the root cause?
- what working behavior must remain unchanged?
- what evidence will prove the fix?

Do not perform a rewrite because the codebase is aesthetically imperfect.

## 5. Preserve good parts

Inventory what is already reliable: persistence, validation, providers, filters, deployment, tests, UI, logging, or domain rules.

Prefer targeted replacement of the failing stage. A strong repair often keeps most of the system and changes the bottleneck.

If replacement is clearly cheaper/safer than repair, explain the boundary being replaced and preserve external contracts where possible.

## 6. Repair in the smallest useful slice

Implement under tests when code tools are available. Typical repairs include:

- correcting stable identity/dedup rules;
- splitting authoritative evidence from search hints;
- moving cheap deterministic rejection before model calls;
- replacing repeated paid discovery with cached/progressive exploration;
- adding query/board/source history so repeated runs progress;
- validating model quotes/structured facts;
- adding missing retries/timeouts/limits;
- separating “needs review” from “accepted” output;
- making logs report the full funnel rather than only success/failure.

## 7. Verify end-to-end

Unit tests are necessary but not sufficient.

Run the actual harmless workflow far enough to answer:

- did the intended source get queried/refreshed?
- did state progress instead of repeat?
- did good candidates survive cheap filters?
- did the model only receive items that needed judgment?
- did the publication/output gate behave correctly?
- did the system produce something useful, or did it merely complete?

If the real bottleneck is discovery/data quality, say so plainly. Do not weaken correctness gates to make a dashboard look populated.

## Completion

Report:

- recovered intended outcome;
- root cause(s) with evidence;
- what was preserved;
- what changed;
- tests and end-to-end verification performed;
- remaining external/data limitations.
