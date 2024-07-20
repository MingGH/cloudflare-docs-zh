---
updated: 2020-09-23
difficulty: Beginner
pcx_content_type: tutorial
title: 从 Vercel 迁移到 Pages
---

# 从 Vercel 迁移到 Pages

在本教程中，你将学习如何将 Vercel 应用程序部署到 Cloudflare Pages。

{{<Aside type="note">}}

你应该已经在 Vercel 上部署了一个现有项目，并希望将其托管在 Cloudflare Pages 上。Cloudflare Pages 目前不支持 Vercel 的无服务器功能等功能。

{{</Aside>}}

## 查找构建命令和构建目录

要将应用程序移至 Cloudflare Pages，你需要找到你的构建命令和构建目录。Cloudflare Pages 将使用这些信息来构建你的应用程序并进行部署。

在 Vercel 控制面板中找到要部署的项目。该项目应配置为从 GitHub 仓库部署。

![在 Vercel 控制面板中选择站点](/images/pages/migrations/vercel-deploy-1.png)

在网站仪表板内，选择**设置**，然后选择**常规**。

![在站点仪表板中选择站点设置](/images/pages/migrations/vercel-deploy-2.png)

找到 "**编译和开发设置**"面板，其中有 "**编译命令**"和 "**输出目录**"字段。如果你使用的是框架，这些值可能不会填写，但会显示框架使用的默认值。保存这些值，以便部署到 Cloudflare 页面。在下图中，**构建命令**为 `npm run build`，**输出目录**为 `build`。

![查找构建命令和输出目录字段](/images/pages/migrations/vercel-deploy-3.png)

## 创建一个新的 Pages 项目

找到构建目录和构建命令后，就可以将项目移动到 Cloudflare 页面。

[入门指南](/pages/get-started/) 将指导你如何将 GitHub 项目添加到 Cloudflare Pages。

## 添加自定义域名

要为你的 Pages 项目使用自定义域，请 [添加一个自定义域](/pages/configuration/custom-domains/)，该自定义域与你当前部署的 Vercel 应用程序相同。当 Pages 完成网站的初始部署后，你需要删除 Vercel 应用程序，以便开始向 Cloudflare Pages 发送请求。

{{<Aside type="note">}}

Cloudflare 不为你的页面项目提供 IP 地址，因为我们不需要 `A` 或 `AA` 记录将你的域名与你的项目链接起来。相反，Cloudflare 使用 `CNAME` 记录。

有关详情，请参阅 [自定义域](/pages/configuration/custom-domains/)。

{{</Aside>}}

## 清理旧应用程序并分配域名

在域名的 DNS 设置中，确保你已将域名的 CNAME 记录从 Vercel 更新到 Cloudflare Pages。更新 DNS 记录后，请求将转到你的 Pages 应用程序。

完成本指南后，你已成功将 Vercel 项目迁移到 Cloudflare Pages。