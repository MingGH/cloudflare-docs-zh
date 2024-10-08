---
_build:
  publishResources: false
  render: never
  list: never
---

* [Enforce granular access control](/waf/rate-limiting-rules/best-practices/#enforcing-granular-access-control) to resources. Includes access control based on criteria such as user agent, IP address, referrer, host, country, and world region.
* [Protect against credential stuffing](/waf/rate-limiting-rules/best-practices/#protecting-against-credential-stuffing) and account takeover attacks.
* [Limit the number of operations](/waf/rate-limiting-rules/best-practices/#limiting-the-number-of-operations) performed by individual clients. Includes preventing scraping by bots, accessing sensitive data, bulk creation of new accounts, and programmatic buying in ecommerce platforms.
* [Protect REST APIs](/waf/rate-limiting-rules/best-practices/#protecting-rest-apis) from resource exhaustion (targeted DDoS attacks) and resources from abuse in general.
* [Protect GraphQL APIs](/waf/rate-limiting-rules/best-practices/#protecting-graphql-apis) by preventing server overload and limiting the number of operations.
