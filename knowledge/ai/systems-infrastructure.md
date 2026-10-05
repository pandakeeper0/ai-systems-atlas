# Systems & Infrastructure

## Scope

The systems layer that makes model/agent behavior reliable at product scale: serving, routing, retries, quotas, caching, observability, availability, isolation, and dependency management.

## Questions to track

- What availability target is appropriate for AI features that depend on model providers?
- How should occasional stochastic failures be distinguished from outage windows?
- What retry and fallback behavior improves UX without hiding systemic problems?
- How should provider/model dependencies affect hard SLA and SLO design?
- Which states and errors should be surfaced to users versus absorbed by the platform?

## Core dimensions

- availability and SLOs
- retries and fallbacks
- rate limits and quotas
- model/provider routing
- prompt/context caching
- concurrency and queueing
- observability and traces
- cost controls
- security and isolation
- degradation strategies

## Related topics

- [Harnesses](harnesses.md)
- [AI Product Design](ai-product-design.md)
