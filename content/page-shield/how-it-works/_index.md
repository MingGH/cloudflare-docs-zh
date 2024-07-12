---
title: How it works
pcx_content_type: concept
weight: 3
meta:
  title: How Page Shield works
  description: Page Shield tracks resources (such as scripts) loaded by your website visitors and provides alerts when it detects new, changed, or malicious resources.
---

# How Page Shield works

Page Shield helps manage resources loaded by your website visitors, including scripts, their connections, and cookies. It can trigger alert notifications when resources change or are considered malicious.

Enabling Page Shield adds a {{<glossary-tooltip term_id="content security policy (CSP)" link="https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP">}}Content Security Policy (CSP){{</glossary-tooltip>}} deployed with a [report-only directive](/page-shield/reference/csp-header/) to collect information from the browser. This allows Cloudflare to provide you with a list of all scripts running on your application and the connections they make to third-party endpoints. Page Shield also monitors ingress and egress traffic for cookies, either set by origin servers or by the visitor's browser.

The **Monitors** dashboard shows the list of active scripts, connections, and cookies. The **All Reported Scripts** and **All Reported Connections** dashboards show the full list of detected scripts and connections in your domain, respectively, including infrequent and inactive ones.

Since Page Shield monitoring is based on sampling, there may be a small delay between deploying a script or cookie and having its data displayed in Page Shield's dashboards.

Enterprise customers with a paid add-on have access to additional classification mechanisms based on threat feeds to determine if a script, or a connection made by a script, is malicious. For more information, refer to [Malicious script and connection detection](/page-shield/how-it-works/malicious-script-detection/).

## Positive security model using policies

Enterprise customers with a paid add-on can create [policies](/page-shield/policies/) to define a positive security model (also known as positive blocking) for resources such as scripts.

When you create policies, Page Shield will generate CSP directives from those policies based on their configuration:

* Log policies will create CSP directives for the `Content-Security-Policy-Report-Only` HTTP header.
* Allow policies will create CSP directives for the `Content-Security-Policy` HTTP header.

For more information, refer to [Policies](/page-shield/policies/).

## Learn more

For more background on Page Shield, refer to our [blog post](https://blog.cloudflare.com/page-shield-generally-available/).