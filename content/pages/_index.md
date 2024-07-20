---
title: 概览
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

通过连接到[你的 Git 提供商](/pages/get-started/git-integration/)、使用[直接上传](/pages/get-started/direct-upload/)或从命令行使用[C3](/pages/get-started/c3/)将预构建资产直接上传到 Pages 来部署你的 Pages 项目。

{{<render file="_non-contract-enablement.md" productFolder="fundamentals" >}}

---

## Features

{{<feature header="Pages Functions" href="/pages/functions/">}}

使用 Pages Functions 部署服务器端代码以启用动态功能，而无需运行专用服务器。

{{</feature>}}

{{<feature header="Rollbacks" href="/pages/configuration/rollbacks/">}}

回滚允许你立即将项目恢复到以前的生产部署。

{{</feature>}}

{{<feature header="Redirects" href="/pages/configuration/redirects/">}}

为您的 Cloudflare Pages 项目设置重定向。

{{</feature>}}

---

## 相关产品

{{<related header="Workers" href="/workers/" product="workers">}}

Cloudflare Workers 提供无服务器执行环境，让你无需配置或维护基础设施即可创建新应用程序或增强现有应用程序。

{{</related>}}

{{<related header="R2" href="/r2/" product="r2">}}

Cloudflare R2 Storage 允许开发人员存储大量非结构化数据，而无需支付与典型云存储服务相关的昂贵出口带宽费用。

{{</related>}}

{{<related header="D1" href="/d1/" product="d1">}}

D1 是 Cloudflare 的原生无服务器数据库。通过导入数据或定义表以及在 Worker 中或通过 API 编写查询来创建数据库。

{{</related>}}

{{<related header="Zaraz" href="/zaraz/" product="zaraz">}}

将第三方工具和服务卸载到云端，提高网站的速度和安全性。

{{</related>}}

---

## 更多资源

{{<resource-group>}}

{{<resource header="Limits" href="/pages/platform/limits/" icon="documentation-clipboard">}}了解适用于您的 Pages 项目的限制（免费计划每月 500 次部署）。{{</resource>}}

{{<resource header="Migration guides" href="/pages/migrations/" icon="reference-architecture">}}从现有托管提供商迁移到 Pages。{{</resource>}}

{{<resource header="Framework guides" href="/pages/framework-guides/" icon="learning-center-book">}}在 Pages 上部署 React、Hugo 和 Next.js 等流行框架。{{</resource>}}

{{<resource header="Developer Discord" href="https://discord.cloudflare.com" icon="logo-Discord">}}与 Discord 上的 Workers 社区联系，提出问题，展示您正在构建的内容，并与其他开发人员讨论该平台。{{</resource>}}

{{<resource header="@CloudflareDev" href="https://x.com/cloudflaredev" icon="twitter">}}在 Twitter 上关注 @CloudflareDev，了解产品公告以及 Cloudflare Workers 的新增功能。{{</resource>}}

{{</resource-group>}}