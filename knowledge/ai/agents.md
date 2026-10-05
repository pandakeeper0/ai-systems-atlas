# Agents

## Working definition

An agent is a system in which a model participates in an iterative loop of observing state, deciding what to do, using tools or other agents, and updating its plan until a task reaches a stopping condition.

## Questions to track

- Where should planning live: model, harness, workflow graph, or policy layer?
- When does a workflow become an agent?
- How should subagents be spawned, isolated, budgeted, and terminated?
- How should long-running and blocking sessions map to user-visible task state?
- Which responsibilities should stay in the orchestrator, and which should be delegated to increasingly capable models?
- How do effort/reasoning settings interact with caching, subagents, and tool use?

## Design dimensions

- autonomy vs determinism
- single-agent vs multi-agent
- blocking vs asynchronous execution
- explicit workflow graph vs model-directed control
- persistent memory vs ephemeral context
- tool policy and permission boundaries
- failure recovery and retry semantics
- user-visible progress and state abstraction

## Related topics

- [Harnesses](harnesses.md)
- [Evaluation](evaluation.md)
- [AI Product Design](ai-product-design.md)
