---
_build:
  publishResources: false
  render: never
  list: never
inputParameters: param1;;param2;;param3
---

- $3 can create up to five different $1 custom nameserver sets. Each nameserver set must have between two and five different nameserver names (`ns_name`), and each name cannot belong to more than one set. For example, if `ns1.example.com` is part of `ns_set 1` it cannot be part of `ns_set 2` or vice versa.
- [Subdomain setup](/dns/zone-setups/subdomain-setup/) or [reverse zones](/dns/additional-options/reverse-zones/) can use $1 custom nameservers as long as they use a different nameserver set (`ns_set`) than their parent, child, or any other zone in their direct hierarchy tree.