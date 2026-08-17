# Claude Code Evidence Map

This page records where the Claude Code system notes come from and how much confidence to assign to each source.

## 1. Official sources — highest confidence

### Claude Code subagents documentation

Use for the current documented contract and supported behavior:

- subagent definition/frontmatter
- model and effort overrides
- skills and MCP configuration
- memory, hooks, permissions and isolation
- background execution
- resume / SendMessage behavior
- context and compaction semantics
- concurrency / nesting limits

URL: https://code.claude.com/docs/en/sub-agents

### Claude Code model/config/environment documentation

Useful for model selection, extended context variants, environment flags and compaction-related controls.

Relevant docs:

- https://code.claude.com/docs/en/model-config
- https://code.claude.com/docs/en/context-window
- https://code.claude.com/docs/en/env-vars
- https://code.claude.com/docs/en/mcp
- https://code.claude.com/docs/en/hooks

### Anthropic engineering: context engineering for agents

Use for the design rationale behind context isolation, compaction, subagent use, and keeping noisy intermediate work out of the lead agent's context.

URL: https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents

### Anthropic engineering: multi-agent research system

Use for orchestration lessons: delegation quality, task decomposition, lead-worker coordination, parallelization, and the cost of poor handoffs.

URL: https://www.anthropic.com/engineering/multi-agent-research-system

## 2. March 2026 source-map reconstruction — strong implementation evidence, not official API

Repository used heavily in this study:

- https://github.com/davccavalcante/claude-code-leaked

The repository reconstructs Claude Code source structure from a March 2026 source-map leak. It is valuable because it exposes function-level implementation and code comments, but it is not an official Anthropic source release and can represent a particular build/feature-gate state.

Key files:

### `src/tools/AgentTool/AgentTool.tsx`

Most important orchestration entry point. Shows:

- Agent tool input schema
- `prompt`, `subagent_type`, model/background/isolation inputs
- agent selection and MCP requirement checks
- fresh vs fork routing
- foreground/background lifecycle
- worktree handling
- task registration
- parent/child abort-controller differences
- result finalization and notification behavior

### `src/tools/AgentTool/runAgent.ts`

Most important child-runtime file. Shows:

- child model resolution
- child transcript routing
- sidechain persistence
- user/system context assembly
- CLAUDE.md / git-status slimming for built-ins
- effort override
- permission and tool scoping
- skill preload
- agent-specific MCP setup and teardown
- hook context injection
- independent query loop
- max-turn handling
- cleanup of child-owned resources

### `src/tools/AgentTool/prompt.ts`

Critical for understanding **how Main Claude is taught to delegate**. It contains the Agent tool instructions that tell the parent:

- fresh agents start without conversation context
- brief them like a smart colleague entering the room
- include goals, prior findings and constraints
- avoid shallow command-style prompts
- do not "delegate understanding"
- use foreground/background according to dependency
- use SendMessage to continue an existing worker

This file is central evidence for the thesis that semantic handoff is performed by the parent model rather than by a separate context-selection algorithm in the harness.

### `src/tools/AgentTool/forkSubagent.ts`

Most important file for fork semantics and prompt-cache engineering. Shows:

- full-context inheritance
- parent system-prompt reuse
- exact tool-pool reuse
- no recursive forking
- placeholder tool results for sibling forks
- child directive construction
- the goal of byte-identical API request prefixes

### `src/Task.ts`

Shows generic task state/lifecycle primitives including:

- task types
- `pending / running / completed / failed / killed`
- task IDs
- timestamps
- output files
- kill semantics

## 3. Runtime reverse engineering — strong behavioral evidence

### Yuyz0112/claude-code-reverse

Repository:

- https://github.com/Yuyz0112/claude-code-reverse

Useful because it observes real Claude Code API traffic rather than relying only on static reconstruction. It supports the model that:

- a fresh subagent receives a newly composed task prompt rather than the parent's full message history
- the subagent runs its own message/API trajectory
- final output is re-integrated into the parent as the Agent tool result

This source is especially useful for validating prompt/context behavior against live requests.

## 4. Official Anthropic/Claude Code GitHub issues — historical/evolution evidence

Repository:

- https://github.com/anthropics/claude-code

Issues are useful for understanding when behavior changed, where older limitations existed, and what users observed before features became documented. They should not override current official docs.

Areas worth tracking:

- per-subagent effort evolution
- MCP visibility / connection bugs
- nested subagent support
- background / steering behavior
- subagent visibility and progress

## 5. Evidence hierarchy for future notes

When sources disagree, use this ordering:

1. **Current official Claude Code docs** — public behavior/contract
2. **Current Anthropic engineering posts** — design rationale
3. **Observed runtime/API behavior** — what the current client actually sends/receives
4. **Source-map reconstruction / reverse-engineered implementation** — internal mechanism and code comments
5. **GitHub issues / community reports** — evolution, bugs, edge cases

Always tag implementation claims that come only from reverse-engineered or leaked/reconstructed source as non-contractual.

## 6. Metadata tags for Atlas retrieval

- system: `claude-code`
- topics:
  - `harnesses/subagents`
  - `harnesses/context-management`
  - `harnesses/orchestration`
  - `harnesses/prompt-cache`
  - `harnesses/task-lifecycle`
  - `harnesses/permissions`
- source types:
  - official-docs
  - engineering-blog
  - reverse-engineered-code
  - runtime-observation
  - github-issue
