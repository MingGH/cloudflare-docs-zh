---
title: Place a waiting room
pcx_content_type: how-to
weight: 2
---

# Place a waiting room

When [configuring a waiting room](/waiting-room/how-to/create-waiting-room/), you need to indicate which pages the waiting room will cover. 

Your waiting room requires at least one hostname and path in its configuration settings. There is an implied wildcard after the path, meaning the waiting room will also apply to any subpaths. When there is an active queue, all new users will enter the queue at any URLs covered by this hostname and path combination. If you have multiple waiting rooms, the waiting room with the most specific subpath takes precedence.

## Apply to multiple hostnames and paths

Advanced Waiting Room customers can apply a single waiting room to multiple hostnames and paths. To do so via the UI, after adding the first hostname and path, select **Add Hostname and Path** and then input the next hostname and path combination you would like the waiting room to cover. If adding more than one hostname and path for a single waiting room, you must also create a unique waiting room cookie by filling out the Custom cookie field. To add multiple hostnames and paths via the API, utilize the `additional_routes` field and customize the cookie suffix with the `cookie_suffix` field.

You cannot add any hostname and path combinations already configured for another waiting room. Hostnames must belong to the zone that the waiting room is configured on.

A single waiting room can be applied to multiple custom hostnames as long as the following is true:
- The apex domain is the same between the custom hostnames
- Each custom hostname is [configured explicitly](#custom-hostnames) in SSL for SaaS setup.

## Custom hostnames

To deploy a waiting room to a custom hostname, the non-wildcard custom hostname must be [configured and active in SSL for SaaS](/cloudflare-for-platforms/cloudflare-for-saas/domain-support/create-custom-hostnames/). Then, [create](/waiting-room/how-to/create-waiting-room/) a Waiting Room and optionally apply it to [multiple hostnames](#apply-to-multiple-hostnames-and-paths) with the same apex domain.

This means that – if you want a waiting room for `hello.example.com` – `hello.example.com` must be an active custom hostname. You will not be able to create a waiting room at `hello.example.com` based on only `example.com` being set up as a custom hostname, even if you have enabled wildcards for this custom hostname in SSL for SaaS setup.

## Create exceptions to waiting room coverage

If there are subpaths or query strings of the path you have configured for your waiting room that you would not like the waiting room to apply to, you can create a [Waiting Room bypass rule](/waiting-room/additional-options/waiting-room-rules/bypass-rules/#common-use-cases) to ensure that traffic is not queued at these parts of your site.