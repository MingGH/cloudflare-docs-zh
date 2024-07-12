---
_build:
  publishResources: false
  render: never
  list: never
---

Your zone's **SSL/TLS Encryption Mode** controls how Cloudflare manages two connections: one between your visitors and Cloudflare, and the other between Cloudflare and your origin server.

```mermaid
flowchart LR
    accTitle: SSL/TLS Encryption mode
    A[Browser] <--Connection 1--> B((Cloudflare))<--Connection 2--> C[(Origin server)]
```