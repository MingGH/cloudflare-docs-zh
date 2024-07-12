---
_build:
  publishResources: false
  render: never
  list: never
---

Cloudflare uses the `__cf_bm cookie` to smooth out the {{<glossary-tooltip term_id="bot score">}}bot score{{</glossary-tooltip>}} and reduce false positives for actual user sessions.

The Bot Management cookie measures a single user's request pattern and applies it to the machine learning data to generate a reliable bot score for all of that user's requests.

For more details, refer to [Cloudflare Cookies](/fundamentals/reference/policies-compliances/cloudflare-cookies/).