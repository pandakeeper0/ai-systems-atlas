# Models

## Scope

Track model capabilities as system primitives rather than as release announcements alone: reasoning behavior, context use, tool use, coding, multimodality, controllability, latency/cost, and interaction with harness settings.

## Questions to track

- What changes when reasoning/effort level changes?
- How do effort settings interact with prompt/context caching?
- Can subagents use independent model/effort settings, and when should they?
- Which capabilities are model-native versus harness-enabled?
- How much orchestration can be removed as model capability improves?
- Which model behaviors remain unstable enough to require system-level guardrails?

## Comparison dimensions

- reasoning depth and reliability
- instruction following
- coding and tool use
- context-window behavior
- cache semantics
- multimodality
- latency and throughput
- cost
- steerability / effort controls
- agentic reliability

## Principle

Record releases only when they change a durable system-design assumption or materially shift what is possible.
