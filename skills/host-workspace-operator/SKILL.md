---
name: host-workspace-operator
description: Use when an Agent Builder workflow needs to inspect, search, modify, or verify repository/workspace files using capabilities provided by the current ChatGPT or Codex host.
---

# Host Workspace Operator

Use the host's native workspace capabilities. This skill does not grant filesystem access and must never pretend unavailable tools exist.

Prefer the narrowest operation:

1. read a known file/range;
2. list when paths are unknown;
3. semantic/content search for concepts;
4. grep/exact search for known terms;
5. patch for focused edits;
6. write for authorized new/replacement files;
7. shell for tests, git, archive, or repository-native commands when narrower tools are insufficient;
8. Python for deterministic parsing, transformations, package checks, and verification.

Read before mutating. Respect repository instructions, preserve unrelated work, keep secrets out of source/logs, and prefer focused changes over broad rewrites.

Write/patch/delete/move/rename and mutating shell commands change state. Only perform them when the user has requested or clearly authorized the change.

After mutations, inspect the changed area and run the relevant tests/verifier when possible. Report what actually ran; never fabricate file access or command execution.
