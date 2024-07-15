---
pcx_content_type: how-to
title: Redirecting *.pages.dev to a Custom Domain
---

# 将 \*.pages.dev 重定向到自定义域

了解如何使用 [批量重定向](/rules/url-forwarding/bulk-redirects/) 将 `*.pages.dev` 子域重定向到你的 [自定义域](/pages/configuration/custom-domains/)。

你可能希望这样做，以确保网站内容只在自定义域上提供，而不是在首次部署 Pages 时自动生成的 `*.pages.dev` 网站上提供。

## 设置

将 `*.pages.dev` 子域重定向到自定义域：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/?to=/:account/pages/view/:pages-project/domains)，选择你的账户。
2. 选择 **工作者和页面**，然后选择你的页面应用程序。
3. 转到**自定义域**，确保你的自定义域已列出。如果没有，请点击**设置自定义域**进行添加。
4. 转到**批量重定向**。
5. [创建批量重定向列表](/rules/url-forwarding/bulk-redirects/create-dashboard/#1-create-a-bulk-redirect-list) 以下列内容为模型(但要根据情况替换数值)：

  {{<example>}}

  | 源 URL | 目标 URL | 状态 | 参数|
  | ---- | ----- | ------------ | ------------ |
  | `*.pages.dev` | `https://example.com` | `301` | <ul><li>保留查询字符串</li><li>子路径匹配</li><li>保留路径后缀</li><li>包含子域</li></ul> |

  {{</example>}}

6. 使用刚才创建的列表 [创建批量重定向规则](/rules/url-forwarding/bulk-redirects/create-dashboard/#2-create-a-bulk-redirect-rule)。

要测试重定向是否成功，请访问你的 `*.pages.dev` 域。如果 URL 现在设置为自定义域，则说明规则已传播。

## 相关资源

- [将 www 重定向到域名 apex](/pages/how-to/www-redirect/)
- [使用批量重定向处理重定向](/rules/url-forwarding/bulk-redirects/)