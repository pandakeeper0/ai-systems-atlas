# Claude Code

Claude Code is one of the primary concrete systems used in this Atlas to study how modern coding-agent harnesses are designed.

## Why study it

The goal is not only to learn Claude Code as a product, but to use its implementation to understand durable harness design ideas: delegation, context propagation, subagent lifecycle, tool/runtime isolation, prompt-cache engineering, permissions, persistence, recovery, and background execution.

## Current deep dives

- [Subagent architecture: context, lifecycle, communication, and design](subagents.md)
- [Source map and evidence index](sources.md)
- [Open questions for the next study session](open-questions.md)

## Working mental model

Claude Code's subagent system currently looks less like a simple `Task(prompt)` helper and more like a small agent runtime created by a parent agent. A subagent can have its own identity/system prompt, model and effort level, tools, MCP servers, skills, hooks, permissions, memory, max-turn limits, worktree isolation, background lifecycle, transcript, and resumable agent ID.

The most important design split is between two context-propagation modes:

1. **Fresh subagent**: starts with a fresh conversational context. The parent model performs semantic compression and writes the delegation prompt.
2. **Fork subagent**: inherits the parent's full conversation/system/tool prefix and receives only a short directive. This trades context isolation for information preservation and prompt-cache reuse.

This distinction is a useful general harness pattern: **semantic handoff vs structural cloning**.

## Key design thesis

Claude Code appears to keep the harness relatively thin in one important place: it does not implement a separate semantic retrieval algorithm that selects parent conversation turns for a normal subagent. Instead, the parent model is taught how to write a good handoff through the Agent tool prompt. The harness then provides the runtime primitives around that model decision: context isolation, tools, permissions, lifecycle, persistence, cancellation, backgrounding, and re-integration.
