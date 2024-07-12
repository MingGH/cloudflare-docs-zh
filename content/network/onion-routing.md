---
pcx_content_type: troubleshooting
source: https://support.cloudflare.com/hc/en-us/articles/203306930-Understanding-Cloudflare-Tor-support-and-Onion-Routing
title: Onion Routing and Tor support
---

# Onion Routing and Tor support

Improve the Tor user experience by enabling Onion Routing, which enables Cloudflare to serve your website’s content directly through the Tor network and without requiring exit nodes.

## Availability

{{<feature-table id="network.onion_routing">}}

## How it works

Due to the behavior of some individuals using the Tor network (spammers, distributors of malware, attackers), the IP addresses of Tor exit nodes may earn a bad reputation, elevating their Cloudflare threat score.

Our [basic protection level](/waf/tools/security-level/) issues challenges to visitors whose IP address has a high threat score, depending on the level chosen by the Cloudflare customer.

One way to address this threat score is to create [custom WAF rules](/waf/custom-rules/). Cloudflare assigns the two-letter code `T1` for Tor.  There's no geographical country associated with these IPs, but this approach lets Cloudflare customers override the default Cloudflare threat score to define the experience for their Tor visitors. Cloudflare updates its list of Tor exit node IP addresses every hour.

The other way to improve the Tor user experience is through Onion Routing. This improves Tor browsing as follows:

-   Tor users no longer access your site via exit nodes, which can sometimes be compromised, and may snoop on user traffic.
-   Human Tor users and bots can be distinguished by our Onion services, such that interactive challenges are only served to malicious bot traffic. 

[Tor Browser](https://tb-manual.torproject.org/about/) users receive an [alt-svc header](https://httpwg.org/specs/rfc7838.html#alt-svc) as part of the response to the first request to your website. The browser then creates a Tor Circuit to access this website using the `.onion` TLD service provided by this header.

You should note that the visible domain in the UI remains unchanged, as the host header and the SNI are preserved. However, the underlying connection changes to be routed through Tor, as the [UI denotes on the left of the address bar](https://tb-manual.torproject.org/managing-identities/#managing-identities) with a Tor Circuit. Cloudflare does not provide a certificate for the `.onion` domain provided as part of alt-svc flow, which therefore cannot be accessed via HTTPS.

## Enable Onion Routing

{{<tabs labels="Dashboard | API">}}
{{<tab label="dashboard" no-code="true">}}

To enable **Onion Routing** in the dashboard:

1.  Log in to your [Cloudflare account](https://dash.cloudflare.com) and go to a specific domain.
2.  Go to **Network**.
3.  For **Onion Routing**, switch the toggle to **On**.

{{</tab>}}
{{<tab label="api" no-code="true">}}

To enable **Onion Routing** with the API, send a [`PATCH`](/api/operations/zone-settings-edit-single-setting) request with `opportunistic_onion` as the setting name in the URI path, and the `value` parameter set to `"on"`.

{{</tab>}}
{{</tabs>}}