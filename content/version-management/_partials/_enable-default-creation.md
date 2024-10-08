---
_build:
  publishResources: false
  render: never
  list: never
---

Once you [enable](/version-management/how-to/enable/) Version Management, Cloudflare will automatically create:

- **Version Zero**, think about this as the configuration of your current zone. Once default environments are created, Version Zero is automatically deployed to them, guaranteeing no disruption in your live traffic. This Version is also permanently editable. In case you decide to disable Zone Versioning, Version Zero will become your zone again. 
- **Global Configuration**, you can find all the configurations here that are not supported by Version Management.

{{<Aside type="warning" header="Important">}}

Any changes made to the **Global Configuration** will immediately apply to your zone and all versions of your zone, affecting live traffic.

{{</Aside>}}

On the Environments page, you can create default environments for **Production**, **Staging**, and **Development**.