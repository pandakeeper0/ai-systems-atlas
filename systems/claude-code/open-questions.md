# Claude Code: Open Questions for Next Study Session

This is the continuation queue for the current Claude Code subagent deep dive.

## Highest priority

1. **Exact parent → child handoff construction**
   - Trace the full path from the main model's `Agent` tool_use block to the first child Messages API request.
   - Identify exactly which system/user/attachment blocks appear on the wire for fresh subagents.
   - Compare fresh subagent, fork subagent, resumed subagent and teammate requests byte-for-byte.

2. **Prompt-cache behavior across parent and child**
   - Which prefixes are cacheable and shared?
   - How does changing subagent model or effort affect cache reuse?
   - Why does fork inherit exact tools, thinking config and rendered system-prompt bytes?
   - Quantify where cache breaks in fresh-agent vs fork paths.

3. **Trajectory persistence and resume**
   - Map the on-disk JSONL layout precisely.
   - Understand agent sidechain parent UUID relationships.
   - Trace `SendMessage` resume: transcript load → new user message → query loop → notification.
   - Determine what happens after main-session compaction and process restart.

4. **Status model and parent-state interaction**
   - Enumerate foreground, background, killed, failed, resumed and backgrounded transitions.
   - Determine which child events alter main UI/session state and which are only task-panel state.
   - Compare sync abort propagation vs async independent cancellation.

5. **Nested subagents**
   - Trace spawn-depth enforcement in current source.
   - Understand whether nested child results bubble only to their immediate parent or have any shared event path.
   - Investigate how nested async agents use the root AppState channel.

## Configuration questions

- Exact precedence among parent model, agent frontmatter model and `Agent(model=...)` override.
- Exact precedence for effort and whether effort changes preserve/break prompt cache.
- How `maxTurns` is counted around tool calls, retries and compaction.
- Whether there are additional hidden token, wall-clock or output-size limits.
- How permissionMode inheritance differs for sync, async, `bubble`, `acceptEdits`, `bypassPermissions` and SDK contexts.
- How `allowedTools`, `tools` and `disallowedTools` interact after MCP tools are merged.

## Skills / MCP / hooks

- Measure token cost of preloading skills vs invoking Skill dynamically.
- Confirm lifecycle and connection reuse for referenced vs inline MCP definitions.
- Trace SubagentStart/SubagentStop hooks and additional-context injection.
- Investigate failure modes when required MCP servers are pending, failed or unauthenticated.

## Background execution

- Trace `/tasks`, TaskOutput and notification plumbing.
- Understand foreground → background transition without losing trajectory.
- Determine how progress summarization works and whether summary generation has its own model calls/context.
- Test behavior when Main is cancelled, compacted, restarted or exits while background agents are running.
- Determine exactly when partial results are preserved on kill/failure.

## Isolation / worktrees

- Trace worktree creation, path translation and cleanup.
- Understand what happens when child changes exist at completion, failure or kill.
- Compare worktree isolation with remote isolation where available.
- Investigate merge/reconciliation policy for parallel editing agents.

## Design questions

- Why does Claude Code put semantic handoff quality in the model/tool prompt instead of a deterministic context-selection layer?
- When is full-context fork cheaper or more reliable than a fresh specialist despite carrying more tokens?
- Which orchestration responsibilities should remain in harness code as models improve?
- What should a general-purpose agent platform copy from Claude Code, and what is specific to coding agents?
- How much of the subagent architecture exists primarily for context quality vs latency/parallelism vs inference economics?

## Suggested next reading path

1. `AgentTool.tsx`
2. `prompt.ts`
3. `runAgent.ts`
4. `forkSubagent.ts`
5. `LocalAgentTask/*`
6. `SendMessageTool/*`
7. session/transcript storage utilities
8. query loop and compaction path
9. prompt-cache tracking / dump-prompts utilities
10. official docs + Anthropic engineering posts as a consistency check
