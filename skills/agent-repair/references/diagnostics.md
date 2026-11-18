# Diagnostic Playbook

## Start with the symptom

### “It runs, but the output is useless”

Inspect the funnel, not just the model.

Count: discovered sources → raw items → cheap-filter survivors → model assessments → accepted/published results. Sample rejections at each boundary. If almost nothing reaches the model, the problem is probably upstream data/discovery or deterministic gates. If many items reach the model but few survive, inspect evidence requirements and model extraction. If many are accepted but users hate them, the product rule/eval is wrong.

### “It keeps finding the same things”

Inspect durable discovery history, query fingerprints, stable provider/source identity, cooldowns, pagination, and whether known sources are refreshed separately from discovery of new sources.

Do not fix this only with larger result counts or a growing blacklist.

### “It reports duplicates”

Define stable identity at the strongest available level: provider ID/canonical URL/event ID, then weaker fallbacks. Check whether identity is computed before or after URL normalization. Ensure retries and separate discovery routes converge on the same key.

### “It loses progress between runs”

Separate in-memory run state from durable state. Verify writes actually happen, timestamps use consistent zones, and later runs query the durable store before spending external credits.

### “The model hallucinates eligibility/facts”

Move the model boundary toward extraction: ask for bounded fields and evidence, then deterministically validate source presence and apply user-specific rules in code.

### “It is too expensive”

Trace cost by stage. Common causes: model called before cheap filters, repeated search/discovery, huge context, repeated assessment of known rejects, unbounded fan-out, flagship model on simple classification.

### “It loops/hangs”

Find every retry/loop/cycle. Require explicit stop condition and maximum. Check quality thresholds for reachability and stale state that prevents progress.

### “Deployment says success but nothing happened”

Inspect the trigger, environment, artifact logs, permissions, secrets presence (not values), outbound network restrictions, and whether the deployed revision matches local code. Add a run summary artifact that reports the actual funnel/outcome.

## Evidence hierarchy

Prefer direct runtime evidence over architectural theory:

1. failing/reproduced behavior;
2. logs and persisted state;
3. tests;
4. code inspection;
5. design docs;
6. assumptions.

When these disagree, investigate the disagreement instead of choosing the prettiest source.

## Repair decision

Patch in place when the architecture is basically sound and failure is localized.

Refactor a boundary when responsibilities are tangled enough that the repair cannot be tested independently.

Replace a subsystem when its runtime model fundamentally conflicts with requirements or repair cost exceeds a clean compatible replacement.

Rewrite the entire system only when most core boundaries are wrong. This is rarer than frustrated developers think.
