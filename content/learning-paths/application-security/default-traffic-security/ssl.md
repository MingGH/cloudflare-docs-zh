---
title: SSL / TLS
pcx_content_type: overview
weight: 5
layout: learning-unit
---

Cloudflare offers a range of SSL/TLS options. By default, Cloudflare offers Universal SSL to all domains, but there are many other options available. Cloudflare offers SSL/TLS for free because we believe it is the [right thing to do](https://blog.cloudflare.com/introducing-universal-ssl). Encryption is foundational to the Internet because it prevents data from being manipulated.

1. [**Universal SSL**](/ssl/edge-certificates/universal-ssl/): This option covers basic encryption requirements and certificate management needs.

2. [**Foundation DNS**](/dns/foundation-dns/): Foundation DNS is an Enterprise option that provides strategically distributed IPs to enhance resiliency, reduced exposure to incidents or software regression and more consistent nameserver assignment. 

3. [**Total TLS**](/ssl/edge-certificates/additional-options/total-tls/): Automatically issues certificates for all subdomain levels, extending the protection offered by Universal SSL.

4. [**Advanced Certificates**](/ssl/edge-certificates/advanced-certificate-manager/): Offers customizable certificate issuance and management, including options like choosing the certificate authority, certificate validity period, and removing Cloudflare branding from certificates.

5. [**Custom Certificates**](/ssl/edge-certificates/custom-certificates/): For eligible plans, customers can upload their own certificates, with the user managing issuance and renewal.

6. [**mTLS Client Certificates**](/ssl/client-certificates/): Cloudflare offers a PKI system, used to create client certificates, which can enforce mutual Transport Layer Security (mTLS) encryption.

7. [**Cloudflare for SaaS Custom Hostnames**](/cloudflare-for-platforms/cloudflare-for-saas/): This feature enables SaaS providers to offer their clients the ability to use their own domains while benefiting from Cloudflare's network. 

8. [**Keyless SSL Certificates**](/ssl/keyless-ssl/): Keyless SSL allows security-conscious clients to upload their own custom certificates and benefit from Cloudflare, but without exposing their TLS private keys.

9. [**Origin Certificates**](/ssl/origin-configuration/origin-ca/): Origin CA certificates from Cloudflare are used to encrypt traffic between Cloudflare and your origin web server. These certificates are created through the Cloudflare dashboard and can be configured with a choice of RSA or ECC private keys and support for various server types.

