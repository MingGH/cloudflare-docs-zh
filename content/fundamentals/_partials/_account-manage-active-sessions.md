---
_build:
  publishResources: false
  render: never
  list: never
---

In the Cloudflare dashboard, you can view a list of active sessions associated with your email address.

Each time your email is used to log in to your Cloudflare account, a session begins. The Cloudflare dashboard provides session information including if the device is currently viewing the dashboard, the IP address, location, device type, browser type, and last active login.

If you notice any suspicious activity, you can also revoke any active sessions.

{{<Aside type="note">}}

By default, the session timeout for the [Cloudflare dashboard](https://dash.cloudflare.com) is 72 hours without any activity.

Some customers can also enforce single-sign on (SSO) by [adding a Dashboard SSO application](/cloudflare-one/applications/configure-apps/dash-sso-apps/).

{{</Aside>}}