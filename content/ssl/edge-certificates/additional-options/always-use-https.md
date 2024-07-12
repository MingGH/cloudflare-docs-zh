---
pcx_content_type: how-to
title: Always Use HTTPS
weight: 15
learning_center:
    title: What is HTTPS?
    link: https://www.cloudflare.com/learning/ssl/what-is-https/
---

# Always Use HTTPS

Always Use HTTPS redirects all your visitor requests from `http` to `https`, for all subdomains and hosts in your application.

{{<Aside>}}
This process does not impact certificate validation. If you use [HTTP DCV](/ssl/edge-certificates/changing-dcv-method/methods/), you can still enable Always Use HTTPS.
{{</Aside>}}

Cloudflare recommends not performing redirects at your origin web server, as this can cause [redirect loop errors](/ssl/troubleshooting/too-many-redirects/).

## Availability

{{<feature-table id="ssl.always_use_https">}}

## Encrypt all visitor traffic

To redirect traffic for all subdomains and hosts in your application, you can enable **Always Use HTTPS**.

{{<Aside type="note">}}
If only some parts of your application can support HTTPS traffic, do not enable **Always Use HTTPS** and use a [dynamic redirect](/rules/url-forwarding/single-redirects/) to selectively perform the redirect to HTTPS. Refer to [Redirect admin area requests to HTTPS](/rules/url-forwarding/single-redirects/examples/#redirect-admin-area-requests-to-https) for an example.
{{</Aside>}}

{{<tabs labels="Dashboard | API">}}
{{<tab label="dashboard" no-code="true">}}

To enable **Always Use HTTPS** in the dashboard:

1. Log in to your [Cloudflare account](https://dash.cloudflare.com) and go to a specific domain.
2. In **SSL/TLS** > **Overview**, make sure that your [SSL/TLS encryption mode](/ssl/origin-configuration/ssl-modes/off/) **is not** set to **Off**. When you set your encryption mode to **Off**, the **Always Use HTTPS** option will not be visible in your Cloudflare dashboard.
3. Go to **SSL/TLS** > **Edge Certificates**.
4. For **Always Use HTTPS**, switch the toggle to **On**.

{{</tab>}}
{{<tab label="api" no-code="true">}}

To enable or disable **Always Use HTTPS** with the API:

1. Make sure that your [SSL/TLS encryption mode](/ssl/origin-configuration/ssl-modes/off/) **is not** set to **Off**.
2. Send a [`PATCH`](/api/operations/zone-settings-edit-single-setting) request with `always_use_https` as the setting name in the URI path, and the `value` parameter set to your desired setting (`"on"` or `"off"`).

{{</tab>}}
{{</tabs>}}

## Limitations

Forcing HTTPS does not resolve issues with [mixed content](/ssl/troubleshooting/mixed-content-errors/), as browsers check the protocol of included resources before making a request. You will need to use only relative links or HTTPS links on pages that you force to HTTPS. Cloudflare can automatically resolve some mixed-content links using our [Automatic HTTPS Rewrites](/ssl/edge-certificates/additional-options/automatic-https-rewrites/) functionality.