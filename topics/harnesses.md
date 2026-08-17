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

## Related topics

- [Agents](agents.md)
- [Systems & Infrastructure](systems-infrastructure.md)
- [Evaluation](evaluation.md)
