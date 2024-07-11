---
title: Overview
type: overview
pcx_content_type: overview
weight: 1
layout: overview
meta:
  title: Cloudflare Pages documentation
---

# Cloudflare Pages

{{<description>}}
创建可立即部署到 Cloudflare 全球网络的全栈应用程序。
{{</description>}}

{{<plan type="all">}}

将您的 Pages 项目部署到 [您的 Git 提供商](/pages/get-started/git-integration/),将预先构建的资产直接上传到 Pages with [Direct Upload](/pages/get-started/direct-upload/)或使用命令行中的 [C3](/pages/get-started/c3)。

{{<render file="_non-contract-enablement.md" productFolder="fundamentals" >}}

---

## Features

{{<feature header="Pages Functions" href="/pages/functions/">}}

Use Pages Functions to deploy server-side code to enable dynamic functionality without running a dedicated server.

{{</feature>}}

{{<feature header="Rollbacks" href="/pages/configuration/rollbacks/">}}

Rollbacks allow you to instantly revert your project to a previous production deployment.

{{</feature>}}

{{<feature header="Redirects" href="/pages/configuration/redirects/">}}

Set up redirects for your Cloudflare Pages project.

{{</feature>}}

---

## Related products

{{<related header="Workers" href="/workers/" product="workers">}}

Cloudflare Workers 提供了无服务器的执行环境,允许您创建新应用程序或扩展现有应用程序,而无需配置或维护基础设施。

{{</related>}}

{{<related header="R2" href="/r2/" product="r2">}}

Cloudflare R2 Storage 允许开发人员存储大量非结构化数据,而无需与典型的云存储服务相关的昂贵的带宽费用。

{{</related>}}

{{<related header="D1" href="/d1/" product="d1">}}

D1 是 Cloudflare 的本地无服务器数据库. 通过导入数据或定义表并在 Worker 或 API 中写下查询来创建数据库。

{{</related>}}

{{<related header="Zaraz" href="/zaraz/" product="zaraz">}}

将第三方工具和服务下载到云中,提高网站的速度和安全性。

{{</related>}}

---

## More resources

{{<resource-group>}}

{{<resource header="Limits" href="/pages/platform/limits/" icon="documentation-clipboard">}}了解适用于您的 Pages 项目的限制（免费计划每月 500 次部署）。{{</resource>}}

{{<resource header="Migration guides" href="/pages/migrations/" icon="reference-architecture">}}Migrate to Pages from your existing hosting provider.{{</resource>}}

{{<resource header="Framework guides" href="/pages/framework-guides/" icon="learning-center-book">}}Deploy popular frameworks such as React, Hugo, and Next.js on Pages.{{</resource>}}

{{<resource header="Developer Discord" href="https://discord.cloudflare.com" icon="logo-Discord">}}Connect with the Workers community on Discord to ask questions, show what you are building, and discuss the platform with other developers.{{</resource>}}

{{<resource header="@CloudflareDev" href="https://x.com/cloudflaredev" icon="twitter">}}Follow @CloudflareDev on Twitter to learn about product announcements, and what is new in Cloudflare Workers.{{</resource>}}

{{</resource-group>}}