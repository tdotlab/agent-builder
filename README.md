# Agent Builder

A skills-only ChatGPT/Codex plugin for building, repairing, and evolving agentic software and automations.

It does not ship an MCP server or require its own model API key. It uses capabilities the current host already provides and only introduces external infrastructure when the system being built actually needs it.

## Skills

- **agent-builder** — greenfield agents, monitors, bots, autonomous/scheduled workflows.
- **agent-repair** — existing systems that are broken, incomplete, unreliable, or technically healthy but failing their real objective.
- **agent-evolve** — extensions, migrations, optimization, production hardening.
- **host-workspace-operator** — safe portable policy for repository/workspace operations.
- **sandbox-python-executor** — deterministic host-Python verification policy.

## Design stance

The default is not “use an agent framework.” The default is “use the smallest architecture that works.” Deterministic automation beats an LLM when rules suffice; one model call beats an agent loop when one judgment suffices; multi-agent systems have to earn their coordination cost.

Version: 0.1.0
