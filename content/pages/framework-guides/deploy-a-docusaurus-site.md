---
pcx_content_type: how-to
title: Docusaurus
---

# Docusaurus

[Docusaurus](https://docusaurus.io)是一款静态网站生成器。它利用 React 的全部功能来创建具有快速客户端导航功能的单页应用程序，从而使网站具有交互性。它提供了开箱即用的文档功能，但也可用于创建任何类型的网站，如个人网站、产品网站、博客或营销登陆页面。

## 建立一个新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 设置项目。C3 将创建一个新的项目目录，启动 Docusaurus 的官方设置工具，并提供即时部署选项。

要使用 `create-cloudflare` 创建新的 Docusaurus 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-docusaurus-app -- --framework=docusaurus
```

`create-cloudflare`将安装其他依赖项，包括[Wrangler](/workers/wrangler/install-and-update/#check-your-wrangler-version) CLI 和任何必要的适配器，并向你提出设置问题。

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

{{<render file="_deploy-via-c3.md" withParameters="Docusaurus">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，并在**设置构建和部署**部分，选择 *Docusaurus* 作为你的**框架预设**。你的选择将提供以下信息。

{{<pages-build-preset framework="docusaurus">}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。

每次你向 Docusaurus 网站提交新代码并将这些更改推送到 GitHub 时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，并在将更改部署到生产之前预览网站的外观。

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Docusaurus">}}