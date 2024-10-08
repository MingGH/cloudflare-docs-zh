---
title: Layer security methods
pcx_content_type: learning-unit
weight: 3
layout: learning-unit
---

Once you have decided on a model (or mixture of models) to secure front door access to your SaaS applications, you can address other common SaaS security areas of concern by layering multiple Cloudflare controls.

## Device posture controls for managed endpoints

You can control endpoint access to your SaaS applications with device posture controls via either [Access for SaaS](/learning-paths/secure-internet-traffic/secure-saas-applications/sso-front-door/) or [WARP enrollment settings](/cloudflare-one/connections/connect-devices/warp/configure-warp/warp-settings/) in Zero Trust. Device posture can determine what constitutes a managed endpoint accessing a SaaS application.

You can also define what constitutes a healthy or semi-healthy managed endpoint and invoke step-up security policies based on potential security concerns. These controls are separate from the controls enacted for unmanaged endpoints.

## Inline security for unmanaged endpoints

For unmanaged endpoints, you can deliver selective inline security like upload/download controls, HTTP filtering, and data loss detection/prevention policies in SaaS applications without the need to install agents.
You can deliver inline security through Browser Isolation, but it requires a [front door control](/learning-paths/secure-internet-traffic/secure-saas-applications/sso-front-door/) (such as Access for SaaS) in order to be applied consistently.

## Browser Isolation

Cloudflare can deliver isolate SaaS apps in via either the WARP client or static links with Clientless Web Isolation.

### WARP client

When your users' devices are enrolled with the WARP client, Cloudflare will transparently isolate browser sessions. In other words, a user's browser will display `example.com`, but Cloudflare will isolate the traffic by rendering it in an isolated browser.

### Clientless Web Isolation

With [Clientless Web Isolation](/cloudflare-one/policies/browser-isolation/setup/clientless-browser-isolation/), Cloudflare will append a static link to the web address. For example, a user's browser going to `example.com` in an isolated session will display `<your-team-name>.cloudflareaccess.com/browser/https://www.example.com`.

When Browser Isolation isolates traffic, Cloudflare can apply the security stack of TLS decryption, HTTP inspection, network inspection, DNS filtering, and DLP policy evaluation to the traffic in the request body. This provides a solution for securing unmanaged endpoint access to sensitive systems and can potentially upgrade traffic from users or services that may have otherwise been deemed as risky. Any method for Browser Isolation attaches the user to your dedicated egress IP addresses so you can apply policies across each method of access consistently.
