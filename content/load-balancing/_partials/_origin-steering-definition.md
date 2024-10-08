---
_build:
  publishResources: false
  render: never
  list: never
---

Endpoint steering customizes how each [pool](/load-balancing/pools/) distributes requests to its associated endpoints.

These distributions are a combination of two properties:

- The endpoint steering [policy](#policies) chosen for your pool.
- The [weights](#weights) assigned to each endpoint.

{{<Aside type="note">}}

If an endpoint [becomes unhealthy](/load-balancing/understand-basics/health-details/), your pool will also re-balance traffic according to its endpoint steering policy.

{{</Aside>}}