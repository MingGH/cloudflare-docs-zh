---
title: Mutual TLS (mTLS)
pcx_content_type: overview
weight: 7
layout: learning-unit
---

Mutual TLS (mTLS) authentication uses client certificates to ensure traffic between client and server is bidirectionally secure and trusted. mTLS also allows requests that do not authenticate via an identity provider — such as Internet-of-things (IoT) devices — to demonstrate they can reach a given resource.

![mTLS sequence diagram](/images/api-shield/api-shield-call-sequence.png)

Support includes [gRPC](https://grpc.io/docs/what-is-grpc/introduction/)-based APIs, which use binary formats such as protocol buffers rather than JSON.

## Creating a mTLS rule

{{<render file="_mtls-create-rule.md" productFolder="api-shield" >}}