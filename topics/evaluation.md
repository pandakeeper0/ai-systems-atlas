# Evaluation

## Purpose

Evaluation turns agent/model changes from intuition into evidence. The useful unit is not only aggregate success rate, but the distribution of failures, regressions, and recoveries across stable use cases.

## Evaluation loop

1. Maintain a stable regression set of representative tasks.
2. Run it on each meaningful model/harness revision.
3. Measure outcome quality, latency, cost, tool behavior, and failure modes.
4. Classify failures by root cause rather than only pass/fail.
5. Test candidate interventions against the same cases.
6. Promote durable findings into system design rules only when evidence supports them.

## Questions to track

- How stable should the regression set be versus continuously refreshed?
- How do we avoid overfitting the harness to a benchmark?
- Which failures should be fixed by prompting, orchestration, model selection, or product UX?
- What is the maintenance cost of intervention layers across model upgrades?
- Which metrics should be hard gates versus diagnostic signals?

## Metrics

- task success / partial success
- regression rate
- failure-category distribution
- recovery after retry
- tool-call correctness
- latency and token cost
- human intervention rate
- user-visible correctness and clarity
