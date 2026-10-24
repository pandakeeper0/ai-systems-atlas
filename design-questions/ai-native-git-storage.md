# AI-native Git Storage · Open Design Question

## Question

AI coding increases commit frequency, ephemeral workspaces, CI fan-out, and repeated repository materialization. Is the classic “every worker has a full local repository” assumption still the right physical storage model?

## Working hypothesis

Keep Git's logical model:

- commit / tree / blob / ref semantics
- content-addressed object IDs
- local branching and snapshot semantics

But decouple it from the assumption that every developer, CI worker, and agent must eagerly hold every object.

A possible direction:

[
	ext{Git semantics}
+
	ext{central/shared CAS}
+
	ext{partial clone}
+
	ext{sparse/lazy materialization}
+
	ext{shared CI/agent caches}
]

This would make local/ephemeral workspaces thinner while preserving Git compatibility.

## Key questions to revisit

- How much of repository growth in AI coding comes from commits/trees vs blobs?
- Which Git operations fundamentally require local history, and which can tolerate remote/lazy objects?
- What is the latency tradeoff between full local object stores and promisor-object fetching?
- Can CI and agent sandboxes share a trusted object cache safely?
- How should partial clone, sparse checkout, worktree, packfiles, commit-graph and multi-pack-index fit together?
- At what point does a centralized object service become a new VCS rather than an optimized Git storage layer?
- What failure/offline/privacy semantics should a thin local repo preserve?

Status: parked for later discussion.
