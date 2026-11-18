# Architecture Selection

Use this reference when the task is complex enough that architecture choice materially affects reliability, cost, or maintainability.

## Start from variance, not branding

The central question is: **where does the task contain uncertainty that deterministic code cannot cheaply resolve?** Put model judgment only there.

A robust system often looks like deterministic acquisition → deterministic cheap filters → model judgment on the ambiguous remainder → deterministic validation/persistence/action.

That shape is preferable to putting every step inside an unconstrained agent loop.

## Complexity ladder

### Deterministic automation

Use when the rules are known and the input space is structured enough to encode them. Examples: uptime checks, feed polling, file transforms, scheduled API synchronization, deduplication, threshold alerts.

### One model call

Use when a single judgment is the only fuzzy step. Examples: classify a release as noteworthy, extract structured facts from a posting, rank a small set of items, draft a message for review.

Require structured output when downstream code depends on the result. Validate model output before using it for external actions.

### Tool-using agent

Use when the system must decide which of several tools to call or adapt its next step based on observations. Keep the tool set small and descriptions precise.

### Stateful workflow

Use when execution must survive process restarts, pause for a person, branch, retry different steps, schedule future work, or maintain durable checkpoints. A workflow engine or graph runtime can be appropriate here, but ordinary explicit state machines are often enough.

### Multi-agent

Use only when work can genuinely be decomposed into semi-independent specialized roles whose separation improves quality, parallelism, or context isolation. Multi-agent coordination adds token cost, latency, state reconciliation, and failure modes. Do not use it for aesthetic reasons.

## Pattern vocabulary

Use these as design descriptions, not framework mandates.

- **Sequential:** fixed stages where each consumes the prior result.
- **Parallel/fan-out:** independent work can run concurrently, followed by fan-in.
- **Router:** a classifier sends inputs to distinct handlers.
- **Loop:** repeat until an explicit quality/condition threshold or hard cap.
- **Generator–critic:** one component proposes; another evaluates; revisions are capped.
- **Plan–execute:** create an explicit plan, then execute steps with state and verification.
- **Human gate:** pause before high-risk, irreversible, ambiguous, or policy-sensitive actions.
- **Map–reduce:** process independent items separately, then aggregate.
- **Subworkflow:** encapsulate a reusable stateful component behind a narrow interface.

## Framework selection

Choose a framework only after the workflow is understood.

Evaluate:

- language/runtime already in the project;
- durable checkpointing needs;
- cycles and conditional routing;
- human pause/resume;
- tool ecosystem;
- deployment target;
- observability;
- persistence model;
- testability;
- vendor/provider lock-in;
- operational complexity.

LangGraph may be useful for durable graph-shaped workflows with cycles and interruption. OpenAI Agents SDK may be useful when an OpenAI-native agent runtime is deliberately desired. CrewAI and other role-oriented frameworks may speed some prototypes. Ordinary Python/TypeScript is often the right answer for smaller systems.

Never select a framework solely because the word “agent” appears in the request.

## Data and retrieval choices

Do not reach for a vector database automatically.

- exact identifiers, counts, joins, date math → structured database/query tools;
- lexical exact-match retrieval → grep/BM25/sparse search;
- semantic similarity → embeddings/vector retrieval;
- mixed query shapes → hybrid retrieval and explicit routing;
- small corpus that fits context → direct inclusion or filesystem search may beat an index;
- cross-document relational reasoning → consider decomposition, structured metadata, or graph-like representations only if needed.

Retrieval quality is an upstream constraint: generation cannot repair evidence that was never retrieved.

## State design checklist

For each durable entity define:

- identity key;
- status lifecycle;
- first-seen / last-seen / last-checked timestamps;
- retry/next-check information where relevant;
- source/evidence pointers;
- whether data is authoritative, derived, cached, or user-entered.

Do not use one boolean such as `processed=true` when the real lifecycle has multiple meaningful states.
