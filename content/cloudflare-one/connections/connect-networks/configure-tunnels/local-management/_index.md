---
pcx_content_type: navigation
title: Locally-managed tunnel
weight: 2
---

# Configure a locally-managed tunnel

If you set up your tunnel [through the CLI](/cloudflare-one/connections/connect-networks/get-started/create-local-tunnel/), the tunnel runs as an instance of `cloudflared` on your machine.  You can configure `cloudflared` properties by modifying [command line parameters](/cloudflare-one/connections/connect-networks/configure-tunnels/tunnel-run-parameters/) or by editing the tunnel configuration file.

The CLI provides a quick way to handle configurations if you are connecting a single service through `cloudflared`. If you are connecting multiple services and you need to configure properties or exceptions for specific origins, you can do so by defining ingress rules in your configuration file.

{{<directory-listing>}}
