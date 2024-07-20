---
pcx_content_type: how-to
title: Vue
---

# Vue

[Vue](https://vuejs.org/)是一个用于构建用户界面的渐进式 JavaScript 框架。Vue 的核心原则是渐进式采用：这使得构建与现有代码并存的 Vue 应用程序变得非常容易。

在本指南中，你将创建一个新的 Vue 应用程序，并使用 Cloudflare Pages 进行部署。你将使用 `vue-cli`，这是一个包含电池的工具，用于生成新的 Vue 应用程序。

## 设置新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 建立新项目。C3 将创建一个新的项目目录，启动 Vue 的官方设置工具，并提供即时部署选项。

要使用 `create-cloudflare` 创建新的 Vue 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-vue-app -- --framework=vue
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository_no_init.md">}}

## 使用 Cloudflare 页面部署

{{<render file="_deploy-via-c3.md" withParameters="Vue">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 "**设置构建和部署**"部分提供以下信息：

<div>

{{<pages-build-preset framework="vue">}}

</div>

配置好网站后，你就可以开始第一次部署了。在部署之前，你应该会看到 Cloudflare 页面正在安装 `vue`、项目依赖项并构建网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Vue 应用程序提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Vue">}}