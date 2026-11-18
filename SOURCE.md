# Source and design provenance

This plugin is a **clean adaptation**, not a one-to-one port of another project.

Design inputs included:

- the user-supplied compact `agent-builder` skill used successfully in earlier ChatGPT experiments;
- the public repository `NikolasMarkou/agent-builder`, whose production-agent guidance informed the decision to include separate build, repair, and evolution workflows;
- current OpenAI/Agent Plugins packaging conventions and host-capability patterns.

The public `NikolasMarkou/agent-builder` repository is distributed under GPL-3.0. This package does not bundle its source code, templates, or reference files. Its skill text and organization were independently written for a framework-neutral ChatGPT/Codex workflow and deliberately differ in product boundary and structure.

Before any public commercial or directory release, perform a final license/similarity review and add whatever attribution or licensing material that review determines is appropriate. Private experimentation does not remove that future review requirement.
