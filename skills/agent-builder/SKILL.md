---
name: agent-builder
description: Use when the user wants to build a new agent, bot, monitor, autonomous workflow, scheduled or event-driven automation, LLM-powered worker, or similar system where autonomous behavior is central. Do not use for an ordinary web app or routine non-agent feature just because code is involved.
---

# Agent Builder

Build the smallest reliable system that achieves the user's outcome. The user's goal is the specification; architecture is your job.

This skill is for **new systems**. If the user points to an existing agent or automation that is broken, incomplete, or failing its real goal, use `agent-repair`. If the existing system basically works and the user wants to extend, migrate, or optimize it, use `agent-evolve`.

If a development-process skill such as Superpowers is installed, follow its planning, debugging, TDD, and verification workflow. This skill supplies agent-domain decisions; it does not replace the host's software-development discipline.

## 1. Frame the real outcome

Establish enough of the following to build without turning the user into the architect:

- desired outcome and success condition;
- what starts the system: manual run, schedule, webhook, message, file change, repository event, API request, or another event;
- inputs and outputs;
- systems it must read from or act on;
- whether actions are read-only, reversible, or externally consequential;
- whether it needs durable state, deduplication, retries, memory, approval, or scheduling;
- where it should run if the user has an existing environment preference.

Infer normal implementation details. Ask only when the missing fact materially changes safety, cost, or feasibility.

## 2. Decide whether this needs an agent

Escalate only when the lower rung cannot reliably handle the task:

1. **Deterministic automation** — scripts, rules, database queries, schedulers, webhooks, parsers.
2. **Single LLM call** — one classification, extraction, ranking, rewrite, or generation step.
3. **Tool-using agent** — dynamic choice among tools is genuinely useful.
4. **Stateful workflow** — branching, retries, checkpoints, long-running state, durable scheduling, or human interruption is required.
5. **Multi-agent system** — independent specialized roles provide a measurable advantage that is worth the extra cost and failure surface.

Do not use agent complexity merely because the user says “agent.”

Read `references/architecture.md` when architecture choice is not trivial.

## 3. Inspect available capabilities before inventing infrastructure

Before hand-writing integrations, inspect what the current host already exposes: plugins/connectors, repository access, browser/search, shell, Python, cloud providers, email, chat platforms, databases, storage, queues, calendars, and schedulers.

Prefer an existing supported capability when it reduces code, credentials, or operational burden. A ChatGPT-hosted skill does not need its own model API key merely to use the model running the conversation.

The system you build may still need credentials after it leaves ChatGPT and runs independently. Keep that distinction explicit.

## 4. Choose architecture and stack neutrally

Prefer standard libraries and the user's existing stack before adding frameworks.

**Do not default to LangChain, LangGraph, CrewAI, OpenAI Agents SDK, or any other framework.** Use one when its runtime model clearly fits the requirements better than a smaller solution.

Typical choices:

- simple local/scheduled automation → ordinary Python/TypeScript + file/SQLite state;
- serverless event handler → platform function + managed scheduler/webhook;
- durable branching/cycles/HITL → a workflow or graph runtime may earn its way in;
- high-volume async work → queue/workflow engine only when retries, concurrency, or durability justify it;
- retrieval-heavy systems → choose retrieval architecture from query shape and corpus needs, not fashion.

When framework-specific facts affect the decision, verify current capabilities instead of trusting a stale matrix.

## 5. Design state deliberately

State is often more important than the model.

Define stable identities for things that must not be processed twice. Separate:

- current run state;
- durable workflow state;
- historical/audit state;
- user-editable configuration;
- secrets.

Prefer simple durable storage for small systems. Use a database only when concurrency, querying, reliability, or scale requires it. Never treat “memory” as one vague bucket.

For monitors and research agents, persistent deduplication and `last_checked` / `next_check` style timestamps are often core correctness, not optional optimization.

## 6. Build the working system

When workspace capabilities are available, create or modify the actual project rather than stopping at pseudocode. Use `host-workspace-operator` for safe workspace behavior and `sandbox-python-executor` when deterministic Python verification materially improves confidence.

A normal deliverable may include:

- focused source files;
- dependency/config manifests;
- `.env.example` with names only, never real credentials;
- persistent storage setup;
- tests and a smoke test;
- scheduler/deployment configuration when required;
- concise run/operations documentation.

If an existing project is supplied as the destination, follow its patterns and extend it rather than building a parallel replacement.

## 7. Design for failure before production

Before calling a system operational, identify its likely failure modes and cap them:

- retries and retry limits;
- timeouts;
- loop/iteration limits;
- concurrency limits;
- deduplication/idempotency;
- partial provider failure;
- malformed model output;
- stale state;
- tool/API rate limits;
- external side effects;
- cost explosion from model/context/fan-out growth.

Read `references/production.md` for production, eval, security, observability, and cost guidance.

## 8. Verify the real outcome

Generated files are not evidence that the system works.

When execution tools exist:

1. run the relevant tests;
2. run a harmless smoke input;
3. exercise the actual data flow far enough to expose integration failures;
4. inspect logs/state/output;
5. fix root causes;
6. repeat until the baseline works or a missing external credential/account blocks further progress.

A technically green pipeline that produces useless results is **not** a successful agent. Verify the user-facing outcome as well as the software path.

## 9. Deployment and ownership

Prefer infrastructure the user already has. Do not add another paid service when GitHub Actions, a current VPS, an existing hosting provider, local scheduling, or a platform-native cron is sufficient.

If deployment requires credentials you cannot access, complete everything else and identify the exact remaining setup step. Do not embed secrets in source or package files.

## Completion

Finish with a compact operational handoff:

- what exists and where;
- how it runs or triggers;
- what state it keeps;
- what was actually verified;
- what, if anything, still needs credentials or external setup.

The working system is the deliverable, not the architecture essay.
