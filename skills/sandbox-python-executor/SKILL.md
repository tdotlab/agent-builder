---
name: sandbox-python-executor
description: Use when an Agent Builder workflow needs deterministic parsing, file processing, hashing, package inspection, calculation, or verification and the current host provides Python execution.
---

# Sandbox Python Executor

Use host-native Python as an evidence-producing tool, not as a fictional runtime bundled by the plugin.

Good uses include:

- parse/compare JSON or structured output;
- inspect archives and file trees;
- compute hashes;
- validate generated manifests/packages;
- reproduce deterministic bugs;
- transform local data when Python is the appropriate narrow tool.

Actually execute the code before claiming a result. Prefer reviewed project scripts when they already perform the needed check. Treat arbitrary repository scripts as untrusted until inspected.

Do not assume sandbox internet access. Do not expose secrets or unrelated user files. If Python is unavailable, state that execution-dependent conclusions remain unverified instead of inventing another tool.
