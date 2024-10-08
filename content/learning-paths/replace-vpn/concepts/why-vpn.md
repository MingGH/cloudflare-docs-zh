---
title: Why should you replace your VPN?
pcx_content_type: overview
weight: 6
layout: learning-unit
---

Many organizations rely on VPNs to protect their data, but for many reasons, VPNs are often not ideal for defending against today's risks.

## Downsides of a VPN

VPNs invoke a perimeter-based security model (often called the [castle-and-moat model](https://www.cloudflare.com/learning/access-management/castle-and-moat-network-security/)), which grant users access to the entire internal network once they login to the VPN client. In castle-and-moat security, it is hard to obtain access from outside the network, but everyone inside the network is trusted by default. The problem with this approach is that once an attacker gains access to the network, they have free rein over everything inside. In the modern digital landscape, where users can access resources from various locations and devices, this concept of a fixed perimeter is no longer effective.

## ZTNA vs. VPNs

[Zero Trust Network Access (ZTNA)](https://www.cloudflare.com/learning/access-management/what-is-ztna/) is the main technology that enables organizations to transition from a traditional VPN configuration to a more secure and modern approach.

**Insider threats** — ZTNA enhances security by assuming that threats may exist both inside and outside the network. Whereas a VPN implicitly trusts all logged-in users, ZTNA emphasizes the [principle of least privilege](https://www.cloudflare.com/learning/access-management/principle-of-least-privilege/) and enforces continuous verification of user identity.

**Network security** — A ZTNA architecture reduces the blast radius of potential threats by facilitating network [micro-segmentation](https://www.cloudflare.com/learning/access-management/what-is-microsegmentation/). Unlike VPNs, which grant users access to the entire corporate network, ZTNA only grants access to a specific application and denies access to all other resources by default.

**Performance** — ZTNA connects users to corporate networks via [the cloud](https://www.cloudflare.com/learning/cloud/what-is-the-cloud/) instead of via on-premise VPN servers. This enables users to connect from anywhere without experiencing performance degradation and integrates more easily with cloud-based infrastructure.
