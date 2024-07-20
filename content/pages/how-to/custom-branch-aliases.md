---
pcx_content_type: how-to
title: 为分支添加自定义域
---

# 为分支添加自定义域

在本指南中，你将学习如何在 Pages 项目中添加一个指向特定分支 (`staging`)的自定义域 (`staging.example.com`)。

这样，你就可以拥有一个自定义域，该域将始终显示 Pages 项目中特定分支的最新版本。

{{<Aside type= "note">}}

目前，只有使用 Cloudflare DNS 时才支持这种设置。

如果你尝试使用外部 DNS 提供商来执行本指南，你的自定义别名将被发送到 Pages 项目的生产分支。

{{</Aside>}}

首先，确保要为其设置自定义域的分支已成功部署。

然后，在页面项目下为所需的自定义域添加一个自定义域，例如 `staging.example.com`。

![按照以下说明访问页面控制面板中的自定义域概览](/images/pages/how-to//pages_custom_domain-1.png)

要做到这一点

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/login)。
2. 在 "账户主页 "中，转到 "**工作者和页面**"。
3. 选择页面项目。
4. 选择 **自定义域**> **设置自定义域**。
5. 输入要使用的域名，如 `staging.example.com`.
6. 选择 **继续**> **激活域**

![选择自定义域名后，系统会要求你激活它。](/images/pages/how-to//pages_custom_domain-2.png)

激活自定义域后，访问 `example.com` 区域的 [DNS](https://dash.cloudflare.com/?to=/:account/:zone/dns)，找到名称为 `staging` 的 `CNAME` 记录，并更改目标，使其包括分支别名。

在这种情况下，将 `your-project.pages.dev` 更改为 `staging.your-project.pages.dev`。

![激活自定义域名后，更改 CNAME 目标，使其包含你的分支名称。](/images/pages/how-to//pages_custom_domain-3.png)

现在，Pages 项目的 `staging` 分支将在 `staging.example.com` 上可用。