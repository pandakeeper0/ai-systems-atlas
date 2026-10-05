# AI Product Design

## Scope

How to turn probabilistic, multi-step AI execution into understandable product behavior without exposing unnecessary implementation complexity.

## Questions to track

- What should a user-visible task state represent when multiple blocking sessions/subagents exist?
- How much internal execution detail should be exposed?
- How should running, waiting-for-user, failed, partially complete, and complete states compose?
- When does exposing precise internal state increase confusion rather than trust?
- How should UX communicate uncertainty, retries, and partial progress?

## Principles to test

- Optimize for the user's actionable mental model, not the internal state machine.
- Separate execution truth from presentation abstraction.
- Avoid making users reason about subagents unless that information changes what they should do.
- Preserve enough detail for debugging and advanced users through secondary views.

## Related topics

- [Agents](agents.md)
- [Harnesses](harnesses.md)
- [Systems & Infrastructure](systems-infrastructure.md)
