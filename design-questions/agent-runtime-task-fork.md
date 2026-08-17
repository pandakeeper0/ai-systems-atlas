# System Design: Forking an Agent Task with a Platform-Owned Message/Tool Model

## Scenario

We build an agent runtime on top of Claude Code (or a similar provider/runtime), but our platform does not expose the provider's conversation representation directly.

Our platform has its own message objects, message IDs, tool-call objects and tool IDs. We want to support **task/session fork**: create a new branch from an existing execution point and let the child continue independently while preserving correct tool/message lineage and, where possible, provider-native optimizations such as prompt-cache reuse.

## Problem statement

Design the semantics, persistence model and materialization path for forking an agent execution when two identity/trajectory layers coexist:

- platform-owned logical messages/tools and IDs;
- provider/runtime-native messages, tool-use IDs, transcript lineage and cache-sensitive serialization.

The fork must be understandable at the product layer while remaining correct at the underlying model/runtime layer.

## Scope

In scope:

- fork point semantics;
- parent/child trajectory lineage;
- message and tool identity;
- provider-native ID preservation;
- replay/materialization;
- tool_use ↔ tool_result pairing;
- prompt-cache preservation;
- retry/resume/fork distinctions;
- immutability and auditability;
- migration across provider/runtime versions.

Initially out of scope:

- UI details for presenting branches;
- merging two divergent agent branches;
- collaborative editing semantics across users;
- cross-provider fork unless it changes the canonical data model.

## Existing constraints

1. The platform owns stable message IDs and tool IDs used by product/business logic.
2. Claude/native runtimes may use IDs that participate in protocol relationships, not merely display identity.
3. A tool result must correctly reference its originating tool use.
4. Claude Code-style fork can depend on byte-identical request prefixes for prompt-cache reuse.
5. Existing history may contain compaction boundaries, sidechain/subagent lineage, retries and partial executions.
6. Fork must not mutate the parent's historical trajectory.

## Required invariants

- **Historical events are immutable.** Forking appends a new branch; it never rewrites the parent history.
- **Tool pairing remains valid.** Every materialized tool result refers to the correct provider-native tool use.
- **Branch lineage is explicit.** A child records its parent and exact fork point.
- **Logical identity is stable.** Platform-level identity survives fork/replay even if an execution attempt receives a new provider-native identity.
- **Native information is not irreversibly discarded.** Provider fields required for replay, resume or cache behavior remain available.
- **Retry, resume and fork are distinct operations.** They must not accidentally share semantics merely because all create additional execution.

## Core identity question

A candidate model separates at least three concepts:

- `logical_event_id` — permanent platform identity for the semantic event;
- `branch_event_id` / execution instance — identity of that event or continuation within a branch/attempt;
- `provider_native_id` — Claude/OpenAI/runtime-specific ID required to reconstruct the native protocol.

A provider-native envelope can coexist with the canonical platform model rather than being flattened away.

## Candidate storage model: event DAG

Treat a conversation/task as an immutable event graph rather than copied arrays of messages.

Example:

```text
E1 → E2 → E3 → E4 → E5
                    ├── F6 → F7   (branch A)
                    └── G6 → G7   (branch B)
```

A branch can store:

- parent branch ID;
- fork event / sequence;
- branch-local delta;
- head event;
- provider/runtime execution metadata.

The common prefix is referenced, not physically duplicated.

## Materializer

Introduce an explicit materialization/compilation layer:

```text
logical event DAG
      +
provider-native envelopes
      +
branch metadata
      ↓
trajectory materializer
      ↓
exact provider/runtime request history
```

The materializer is responsible for reconstructing ordering, roles, content blocks, tool-use/result pairing, provider IDs, system context and any cache-sensitive representation.

## Two fork guarantees

### Semantic fork

The child receives semantically equivalent history and all protocol relationships remain correct. Exact serialized bytes may differ.

### Cache-preserving fork

In addition to semantic correctness, the provider request prefix is preserved as closely as required for native prompt-cache reuse.

These should be explicit product/runtime guarantees rather than accidentally conflated.

## Key design questions

1. What exactly is a legal fork point: any logical event, only completed model turns, or only provider-valid message boundaries?
2. Should shared-prefix events keep the same branch event IDs, or should branch membership be represented separately from event identity?
3. Which Claude/native IDs must be preserved for protocol correctness?
4. Which IDs affect prompt-cache identity even when they do not affect protocol correctness?
5. Can the platform reconstruct a byte-identical native prefix after normalizing messages into its own schema?
6. Should the native transcript be the replay substrate while the platform model is a projection, or should the canonical model be fully compilable back into native form?
7. How do compaction boundaries affect fork? Can a child fork from pre-compaction history when the active parent no longer carries it in context?
8. How do subagent sidechains behave when the main task is forked? Are they shared immutable evidence, copied, detached or resumable from both branches?
9. What happens to in-flight tool calls at the fork point?
10. How do retry, resume and fork interact with idempotency and duplicate external side effects?
11. What happens when the provider/runtime changes serialization or native ID semantics between versions?
12. How much provider-specific state should leak into the canonical platform model?

## Failure modes to investigate

- regenerated native tool IDs break tool-result references;
- normalized serialization silently destroys prompt-cache hits;
- parent history is mutated during fork;
- replay re-executes side-effecting tools;
- child inherits an incomplete assistant/tool turn;
- compaction makes an old fork point impossible to reconstruct;
- provider-specific fields are dropped and later turn out to be required for resume;
- logical IDs are overloaded as execution-attempt IDs, making retries ambiguous;
- two branches incorrectly share mutable runtime state.

## Reference system: Claude Code

Claude Code's fork/subagent implementation is useful because it exposes several relevant principles:

- fresh subagents use semantic handoff rather than copying parent conversation history;
- fork-style agents can structurally inherit the parent trajectory;
- provider/native trajectory details are preserved to maximize prompt-cache prefix reuse;
- subagent trajectories are persisted as sidechains rather than flattened into the main transcript;
- resume, background lifecycle and cancellation are modeled separately from a one-shot tool call.

These behaviors are references, not requirements for our runtime. The design question is which invariants are general and which are Claude-Code-specific optimizations.

## Current hypothesis

Prefer **canonical logical model + provider-native envelope + immutable event DAG + explicit materializer**.

Do not prematurely erase provider-native semantics. Platform objects should support product-level portability, while native trajectory data remains available as the execution/replay substrate until we have proven the canonical representation can losslessly reconstruct every required behavior.

## Experiments before deciding

- Capture a real Claude Code parent request and forked-child request and diff them byte/block-wise.
- Rewrite the same trajectory through the platform serializer and measure cache-hit behavior.
- Test forks before/after tool calls and around compaction boundaries.
- Test whether regenerated tool IDs remain protocol-valid and quantify cache impact.
- Test process restart + resume + fork combinations.
- Classify every native field as: correctness-critical, cache-critical, lineage/debug-only, or disposable.

## Related study

- `systems/claude-code/subagents.md`
- `systems/claude-code/open-questions.md`
- `systems/claude-code/sources.md`
