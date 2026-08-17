# AI Systems Design Questions

This directory turns implementation details learned from real AI systems into reusable system-design exercises.

The goal is not to collect interview trivia. Each question should start from a realistic product/runtime scenario, make the boundaries and constraints explicit, and force architectural trade-offs to be discussed.

## Why this exists

Studying Claude Code, Codex, DeepSeek and other agent systems is most useful when their design choices can be transferred into our own applications, extensions and runtimes. A good design question bridges those two layers:

1. **Observed system design** — how an existing system solves the problem.
2. **Underlying constraints** — why that design may exist.
3. **Our design problem** — what changes when our product has different abstractions or constraints.
4. **Decision framework** — invariants, alternatives, trade-offs and failure modes.

## Standard structure for each question

Each design question should contain:

- **Scenario** — the concrete system/product situation.
- **Problem statement** — exactly what must be designed.
- **Scope / non-goals** — what is and is not part of the problem.
- **Existing constraints** — APIs, storage model, provider behavior, compatibility, latency, cost, safety, etc.
- **Required invariants** — properties the solution must never violate.
- **Key questions** — the decisions that need to be made.
- **Candidate designs** — multiple plausible architectures rather than one premature answer.
- **Trade-offs / failure modes** — correctness, complexity, performance, cache, recovery, observability, migration.
- **Reference systems** — how Claude Code/Codex/etc. approach related problems and what can or cannot be copied.
- **Open experiments** — what must be tested before making the decision.
- **Current hypothesis** — our best current answer, explicitly allowed to evolve.

## Questions

- [Forking an agent task with a platform-owned message/tool model](./agent-runtime-task-fork.md)

## Principle

A design question is successful when it remains useful even after the implementation that inspired it changes. We want to preserve the constraints, reasoning and trade-offs—not merely today's code path.
