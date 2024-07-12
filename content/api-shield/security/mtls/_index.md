---
pcx_content_type: concept
title: Mutual TLS (mTLS)
weight: 7
---

# Mutual TLS (mTLS)

{{<render file="_mtls-api-shield-support.md">}}

{{<glossary-tooltip term_id="mTLS (mutual TLS)">}}Mutual TLS (mTLS){{</glossary-tooltip>}} authentication uses client certificates to ensure traffic between client and server is bidirectionally secure and trusted. mTLS also allows requests that do not authenticate via an identity provider — such as Internet-of-things (IoT) devices — to demonstrate they can reach a given resource.

![mTLS sequence diagram](/images/api-shield/api-shield-call-sequence.png)

Support includes [gRPC](https://grpc.io/docs/what-is-grpc/introduction/)-based APIs, which use binary formats such as protocol buffers rather than JSON.

## Configure

For help setting up mTLS for one or more hosts using the dashboard, refer to [Configure mTLS](/api-shield/security/mtls/configure/).

## Availability

All Cloudflare plans can set up mTLS with a Cloudflare-managed certificate authority (CA). Enterprise customers can [upload up to five non-Cloudflare CAs](/ssl/client-certificates/byo-ca/). For higher limits, contact your account team.

## Limitations

When using Yubikeys, the browser may prompt for unlocking the key due to a problem in Yubikey's PKCS#11 library.