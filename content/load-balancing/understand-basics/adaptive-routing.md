---
pcx_content_type: concept
title: Adaptive routing
weight: 18
---

# Adaptive routing

Adaptive routing controls features that modify the routing of requests to pools and endpoints in response to dynamic conditions, such as during the interval between active health monitoring requests. {{<render file="_zero-downtime-failover-retry-on-error.md" productFolder="fundamentals">}}

## Failover across pools

When there are no healthy endpoints in the same pool, failover across pools extend the zero-downtime failover of requests to healthy endpoints in alternate pools according to the failover order defined by traffic and endpoint steering.

### Enable failover across pools

1. Log in to [Cloudflare dashboard](https://dash.cloudflare.com/) and select your account and domain.
2. Select **Traffic** > **Load Balancing**.
3. Navigate to your Load Balancers and select **Edit**.
4. From **Adaptive Routing**, enable **Failover across pools**.