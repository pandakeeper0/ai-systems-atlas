# Claude Code Subagents: Context, Lifecycle, Communication, and Design

_Last updated: 2026-08-18_

## Scope

This note captures the current understanding of Claude Code subagents from official Claude Code documentation, Anthropic engineering material, runtime reverse engineering, and the March 2026 source-map reconstruction of Claude Code. Because the reconstructed source is not an official source release, implementation details from it should be treated as strong observational evidence rather than a stable public API contract.

## 1. Runtime model

A Claude Code subagent is best understood as a child agent runtime rather than a single model call. It can own its own:

- system prompt / identity
- model and effort
- tools and disallowed tools
- permission mode
- skills
- MCP servers
- hooks
- persistent memory
- maximum turns
- transcript and resumable agent ID
- background/foreground lifecycle
- optional worktree isolation

The parent and child therefore share some operational infrastructure but do not necessarily share conversational state.

## 2. Fresh subagent vs fork subagent

### Fresh subagent

A normal subagent does **not** inherit the parent's full conversation history. The parent Claude emits an `Agent` tool call containing a `prompt`; the harness converts that prompt directly into the child's initial user message.

Conceptually:

```text
Parent conversation
      |
      | parent model decides what matters
      v
Agent({ prompt: "compressed handoff" })
      |
      v
Fresh child context
```

This means context selection for a normal subagent is primarily performed by the **parent model**, not by a separate retrieval/ranking algorithm in the harness.

### Fork subagent

The newer fork path does the opposite. It carries the parent conversation into the child and adds a short directive. The reconstructed implementation passes parent messages as `forkContextMessages`, filters incomplete tool calls, and prepends those messages to the child trajectory.

```text
Parent conversation ----------------------+
                                           v
                                  Fork child context
                                           +
                                      short directive
```

This gives Claude Code two different context-propagation primitives:

- **semantic compression**: fresh subagent
- **structural cloning**: fork

## 3. How the parent writes the handoff

The interesting part is that Claude Code teaches the **main model** how to delegate through the Agent tool description.

The reconstructed `AgentTool/prompt.ts` instructs the parent to treat a fresh worker like a smart colleague who has just entered the room. The parent should explain:

- the goal and why it matters
- what has already been learned
- what has already been ruled out
- relevant surrounding context
- expected output shape/length
- concrete paths or facts when they matter

It explicitly discourages phrases equivalent to "based on what we already found" because the child has not seen that history. It also warns the parent not to "delegate understanding": synthesis should happen in the parent before the handoff when possible.

This reveals an important design choice:

> The harness defines the delegation protocol; the model performs semantic context compression.

The harness does not appear to run an extra semantic-search stage over the parent transcript for ordinary subagents.

## 4. Context assembled automatically by the harness

Although the parent decides the task-specific semantic handoff, Claude Code still injects structural/runtime context into the child. The reconstructed `runAgent.ts` shows the child context being assembled from several sources:

- agent-specific system prompt
- environment details
- delegation prompt
- user/system context such as CLAUDE.md and session information
- preloaded skills
- MCP servers and MCP tools
- SubagentStart hook context
- tool/permission configuration

For read-only built-ins such as Explore and Plan, current code contains explicit context slimming: CLAUDE.md and stale parent-session git status can be omitted to save tokens. This is a notable example of fleet-scale context-cost engineering rather than merely local correctness.

## 5. Model calls and context window

A normal subagent runs an independent model loop with its own message history. It is not simply an extension of the parent's next API call.

Important nuance:

- **logical agent loop** is independent
- **physical OS process** may or may not be independent

Claude Code has multiple execution forms (local agent, remote agent, in-process teammate, etc.). The API trajectory is still logically separate.

The effective context-window limit follows the selected model/context variant. A subagent also compacts independently of the main conversation.

## 6. Effort and compute routing

A subagent can override effort independently. In the reconstructed `runAgent.ts`, effective effort is selected from the agent definition when present; otherwise it inherits session effort.

This lets orchestration route not only by model but also by reasoning intensity:

```text
Task
  -> agent type
  -> model
  -> effort
  -> max turns
```

That is a more granular compute-allocation mechanism than model routing alone.

## 7. Skills

Agent frontmatter can preload skills. The reconstructed runtime resolves skill names, loads their full prompt content, and inserts them into the child's initial messages as meta user messages.

This is important: preloading a skill is not just an ACL. It is a **context injection decision**.

The child may also still use dynamic Skill tooling when available, so "preloaded" and "permitted" are conceptually different.

## 8. MCP servers

Subagents can define MCP requirements or MCP servers.

The reconstructed implementation distinguishes:

- **referenced existing server**: reuse/memoize an existing connection
- **inline agent-specific definition**: connect for this agent and clean it up at agent termination

This provides capability isolation and can reduce main-context/tool-schema pollution. The child can receive a capability surface that the parent does not need to carry directly.

## 9. Tools and permissions

The worker tool pool is assembled separately from the parent's. Parent session approvals are deliberately prevented from leaking into the child's session allow-rules when an explicit child allowlist is present.

Background agents that cannot show permission UI are configured to avoid interactive permission prompts. This makes async execution safe from deadlocking on an unseen prompt.

A useful general design principle is visible here:

> Task steering and capability escalation are separate control planes.

The parent can tell a child what to do, but that does not automatically grant additional permissions.

## 10. Trajectory and persistence

The subagent's full trajectory is not recorded inline in the main thread. Claude Code persists it as a **sidechain transcript**, keyed by agent ID.

The reconstructed loop records:

- initial messages
- assistant/user messages
- progress messages
- compact boundaries
- tool-use/tool-result trajectory

The main thread generally retains only the `Agent` tool invocation plus the eventual result/notification, while the noisy internal exploration remains in the sidechain.

This is one of the most important context-engineering benefits of subagents.

## 11. Resume and reuse

A resumable agent ID behaves more like a persistent conversation branch than a one-shot function call ID.

A later `SendMessage(to=<agentId>)` can continue the same child with its prior trajectory preserved. One-shot built-ins such as Explore/Plan are intentionally treated differently and are not intended as long-lived specialists.

Design interpretation:

- disposable workers optimize for cheap exploration
- resumable workers optimize for continuity and specialist state

## 12. Parent-child communication

### Foreground

Foreground execution behaves like a blocking tool call:

```text
Main turn
  -> Agent tool call
  -> child runs N turns
  -> final child result
  -> Agent tool_result
  -> main continues
```

The reconstructed code collects child messages via an async iterator and finalizes them into an Agent tool result.

### Background

Background agents register as independent tasks and return immediately with an agent/task ID and output file. Completion later produces a notification that re-enters the main conversation in a later turn.

This means **main-interaction state** and **child-task state** are separate dimensions. The main thread can be idle or talking to the user while a child remains `running`.

### Mid-run steering

Named/addressable workers can receive `SendMessage` instructions while running. This gives the parent a control channel without forcing the child trajectory into the main context.

## 13. State machine and cancellation

Task state is explicitly represented as:

```text
pending -> running -> completed
                   -> failed
                   -> killed
```

Each task has its own task ID, timing, output file and lifecycle data.

The cancellation semantics differ by execution mode:

- synchronous child: normally shares the parent's abort controller
- asynchronous/background child: gets an unlinked abort controller

The reconstructed code explicitly comments that background agents should survive when the user presses ESC to cancel the main thread. They are killed explicitly instead.

This is an important semantic choice: background work is a first-class independent task, not merely a child stack frame.

When a background child is killed, the runtime attempts to preserve a partial result and emit a killed notification rather than silently discarding all work.

## 14. Backgrounding a foreground agent

A foreground agent can be transitioned into background execution. The implementation registers foreground agents as tasks early, races message production against a background signal, then transfers execution into an async lifecycle while preserving progress/messages.

This is more sophisticated than simply restarting the task in the background and shows that Claude Code models foreground/background as a lifecycle transition of the same logical work item.

## 15. Limits and stopping rules

Relevant controls include:

- per-agent `maxTurns`
- concurrency limits for running subagents
- spawn-depth limits for nested subagents
- tool-specific timeouts
- environment/feature flags controlling background execution

The reconstructed runtime also waits for required MCP servers for a bounded period before failing the spawn when dependencies cannot become ready.

A general-purpose wall-clock timeout does not appear to be the primary public subagent control; turn count and task/tool lifecycle are more central.

## 16. Resource ownership and cleanup

The child's lifecycle owns resources it creates. On exit, the reconstructed runtime cleans up agent-specific resources including:

- agent-created MCP connections
- agent-scoped hooks
- prompt-cache tracking
- cloned file-state caches
- tracing registrations
- transcript routing state
- agent TODO state
- background shell tasks spawned by that agent
- monitor/MCP tasks spawned by that agent

This exposes another useful harness principle:

> Resource lifetime should follow agent lifetime.

It prevents orphan processes/connections from accumulating in long sessions.

## 17. Prompt-cache engineering in fork mode

Fork mode contains unusually explicit cache engineering.

To maximize cache reuse across several sibling forks spawned in one parent assistant message, Claude Code keeps an identical parent prefix for each child:

1. copy the full parent assistant message, including all tool-use blocks
2. synthesize identical placeholder tool results for every sibling tool call
3. add the child-specific directive only at the end

The reconstructed code describes the goal as **byte-identical API request prefixes**.

It also threads the parent's already-rendered system prompt bytes into the fork child rather than rebuilding them, because tiny changes in generated system-prompt content could break the cache prefix.

This is a strong example of harness code being shaped by inference economics.

## 18. Why both fresh workers and forks exist

### Fresh worker

Strengths:
- clean context
- strong specialization
- independent perspective
- can route to a different model
- noisy exploration stays outside parent

Costs:
- handoff information can be lost
- parent must write a good brief
- less cache sharing with parent

### Fork

Strengths:
- almost no handoff information loss
- parent context is immediately available
- strong prompt-cache sharing
- very cheap to express a narrow directive

Costs:
- carries the parent's context bulk/noise
- less independence
- less useful when a truly fresh specialist view is desired

The two mechanisms therefore solve different problems rather than representing old/new versions of the same feature.

## 19. Design lessons worth generalizing

1. **Subagents are primarily a context-management primitive, not just a parallelism primitive.**
2. **Let the model perform semantic handoff; let the harness enforce runtime boundaries.**
3. **Keep child trajectory out of parent context unless it is needed.**
4. **Separate interaction state from background-task state.**
5. **Make background lifecycle/cancellation explicit.**
6. **Treat agent ID as persistent branch identity when reuse matters.**
7. **Bind capability, permission and resource ownership to the child runtime.**
8. **Prompt-cache economics can materially shape orchestration architecture.**
9. **Support both compressed handoff and full-context cloning because they optimize different trade-offs.**

## 20. Confidence / caveats

Official docs and Anthropic engineering posts are the source of truth for documented behavior. The March 2026 source-map reconstruction is especially useful for understanding call paths and hidden design intent, but it should not be treated as an official stable API. Feature-gated paths such as fork/coordinator behavior can change quickly.

See [sources.md](sources.md) for the evidence map and [open-questions.md](open-questions.md) for the next investigation targets.
