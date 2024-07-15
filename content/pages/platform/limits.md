---
pcx_content_type: concept
title: Limits
weight: 1
---

# 限制

以下是 Cloudflare 免费计划遵守的限制。有关移除这些限制的详细信息，请参阅 [Cloudflare 计划](https://www.cloudflare.com/plans) 页面。

{{<render file="_limits_increase.md" productFolder="workers">}}

## Builds

每次向 Git 仓库推送新代码时，Pages 都会构建并部署你的网站。免费计划每月最多可构建 500 次。如果需要更多构建次数，请参阅 [定价](https://pages.cloudflare.com/#pricing)中的专业版和商业版计划。

构建将在 20 分钟后超时。

## 自定义域

根据你的 Cloudflare 计划类型，页面项目仅限于特定数量的自定义域。此限制以每个项目为单位。

| 免费版 | 专业版 | 商务版 | 企业版|
| ---- | --- | -------- | ---------- |
| 100  | 250 | 500      | 500[^1]    |

[^1]:如果你需要更多自定义域，请联系你的客户团队。

## 文件

页面会将你网站上的每个文件上传到 Cloudflare 的全球分布式网络，为访问你网站的每个用户提供低延迟体验。Cloudflare Pages 网站最多可包含 20,000 个文件。

## 文件大小

单个 Cloudflare Pages 站点资产的最大文件大小为 25 MiB。

## Headers

一个 `_headers` 文件最多可包含 100 条标题规则。

在`_headers`文件中，单个标题最多可包含 2000 个字符。要管理更大的headers，建议使用 [Pages Functions](/pages/functions/)。

## 预览部署

你可以在项目上同时激活不限数量的 [预览部署](/pages/configuration/preview-deployments/)。

## 重定向

一个 `_redirects` 文件最多可包含 2,000 个静态重定向和 100 个动态重定向，合计 2,100 个重定向。如果需要的重定向次数超过了`_redirects`文件的支持范围，建议使用[批量重定向](/pages/configuration/redirects/#surpass-_redirects-limits)。

## 用户

通过 Cloudflare 控制面板，你的 Pages 网站可由无限多的用户管理。请注意，这与你的 Git 项目无关--你可以通过它管理公共和私有仓库、打开问题和接受拉取请求，而不会影响你的 Pages 网站。

## 项目

为了防止滥用，Cloudflare Pages的软限制为 100 个项目。如果你需要提高此限制，请联系你的 Cloudflare 账户团队或使用本页顶部的限制增加申请表。

为了防止滥用服务，如果你在短时间内部署了大量应用程序，Cloudflare 可能会暂时禁止你创建新的页面项目。如果你需要增加此限制，请联系支持部门。