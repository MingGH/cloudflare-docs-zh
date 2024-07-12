---
title: Configuration settings
pcx_content_type: reference
weight: 1
---

# Configuration settings

## CSP reporting endpoint

When enabled, Page Shield uses a {{<glossary-tooltip term_id="content security policy (CSP)">}}Content Security Policy (CSP){{</glossary-tooltip>}} [report-only HTTP header](/page-shield/reference/csp-header/) to gather information about all the scripts running on your application.

By default, reports are sent to a Cloudflare-owned endpoint:

```txt
https://csp-reporting.cloudflare.com/cdn-cgi/script_monitor/report?<QUERY_STRING>
```

Enterprise customers with a paid add-on can change the reporting endpoint so that the CSP reports are sent to the same hostname:

```txt
<YOUR-HOSTNAME>/cdn-cgi/script-monitor/report?<QUERY_STRING>
```

### Prerequisites for using the same hostname for CSP reports

Using the same hostname for CSP reporting may interfere with other Cloudflare products. Before selecting this option, ensure that your Cloudflare configuration complies with the following:

- No rate limiting rules match the `cdn-cgi/*` URL path
- No WAF custom rules match the `cdn-cgi/*` URL path

### Configure the reporting endpoint

To configure the CSP reporting endpoint:

1.  Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/), and select your account and domain.
2.  Go to **Security** > **Page Shield** > **Settings**.
3.  Under **Reporting endpoint**, select **Cloudflare-owned endpoint** or **Same hostname**.
4.  Select **Apply settings**.

## Connection target details

When connection targets are reported to Cloudflare, their URIs can sometimes include sensitive data such as session ID.

By default, Page Shield will only check the domain against malicious threat intelligence feeds. You can choose to let Page Shield use the full URI when analyzing the connections made from your domain's pages. Any sensitive data present in the URI will be logged in clear text, and any user with access to the connection monitor dashboard will be able to view it.

### Configure the connection target details to use

1.  Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/), and select your account and domain.
2.  Go to **Security** > **Page Shield** > **Settings**.
3.  Under **Connection target details**, select **Log host only** to analyze only the hostname or **Log full URI** to use the full URI in Page Shield.
4.  Select **Apply settings**.

## Turn off Page Shield

When you turn off Page Shield, you lose visibility on the scripts running on your zone, the outbound connections made from pages in your domain, and cookies detected in HTTP traffic.

To turn off Page Shield:

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/), and select your account and domain.
2. Go to **Security** > **Page Shield** > **Settings**.
3. Select **Disable Page Shield**.

{{<Aside type="note">}}
Turning off Page Shield will also turn off all [policies](/page-shield/policies/). After turning on Page Shield again, you must turn on any required policies.
{{</Aside>}}