# Harnesses

## Working definition

The harness is the runtime and control layer around a model: context construction, tool execution, permissions, state, retries, subagents, budgets, compaction, observability, and task lifecycle.

## Questions to track

- Which capabilities belong in the harness versus the model?
- How thin should the orchestrator stay as models improve?
- How should prompt/context caching interact with execution settings?
- How should subagents inherit or override model, effort, tools, and context?
- What state machine is appropriate for blocking sessions and multi-session tasks?
- How should retries, resumability, and partial failures be represented?

## Components

- context assembly and compaction
- model routing and reasoning/effort controls
- tool registry and execution
- permissions and sandboxing
- session/task state
- subagent orchestration
- retries and recovery
- telemetry and traces
- budgets, timeouts, and stopping rules

## Concrete systems / deep dives

- [Claude Code system hub](../systems/claude-code/README.md)
  - [Subagent architecture: context, lifecycle, communication, and design](../systems/claude-code/subagents.md)
  - [Evidence/source map](../systems/claude-code/sources.md)
  - [Open questions](../systems/claude-code/open-questions.md)

The Claude Code study is currently the first concrete system used to extract general harness principles. In particular, it is useful for studying semantic handoff vs full-context forks, sidechain trajectories, background task lifecycle, prompt-cache-aware orchestration, per-agent capabilities, and resource ownership.

## Related topics

- [Agents](agents.md)
- [Systems & Infrastructure](systems-infrastructure.md)
- [Evaluation](evaluation.md)
