---
_build:
  publishResources: false
  render: never
  list: never
---

With [apex proxying](/cloudflare-for-platforms/cloudflare-for-saas/start/advanced-settings/apex-proxying/), SaaS customers need to create an `A` record for their hostname that points to the IP prefix allocated to the SaaS provider's account.

```txt
example.com.  60  IN  A   192.0.2.1
```

{{<Aside type="note">}}

For [BYOIP](/byoip/) customers, Cloudflare automatically enables the Apex Proxy Access feature on your BYOIP block, which allows Custom Hostnames to be activated via [Apex proxying](/cloudflare-for-platforms/cloudflare-for-saas/domain-support/hostname-validation/realtime-validation/#apex-proxying) when Authoritative DNS for a customer's hostname targets any IP addresses in your BYOIP block.


{{</Aside>}}
