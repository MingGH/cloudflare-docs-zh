---
pcx_content_type: how-to
title: Static deployment
meta:
  description: 部署静态 Next.js 站点
---

# 部署静态 Next.js 站点

{{<Aside type="note">}}

除非你有静态导出的特定用例，否则请勿使用本指南。Cloudflare 建议使用 [Deploy a Next.js site](/pages/framework-guides/nextjs/deploy-a-nextjs-site/) 指南。

{{</Aside>}}

[Next.js](https://nextjs.org)是一个用于创建网站和应用程序的开源 React 框架。在本指南中，你将创建一个新的 Next.js 应用程序，并使用 Cloudflare Pages 进行部署。

本指南将指导你如何使用 [static exports](https://nextjs.org/docs/app/building-your-application/deploying/static-exports) 部署静态站点 Next.js 项目。

{{<render file="_tutorials-before-you-start.md">}}

## 选择你的 Next.js 项目

如果已经有了要部署的 Next.js 项目，请确保该项目已[配置为静态导出](https://nextjs.org/docs/app/building-your-application/deploying/static-exports)，并更改为其目录，然后继续下一步。否则，请使用 `create-next-app` 创建一个新的 Next.js 项目。

```sh
$ npx create-next-app --example with-static-export my-app
```

创建项目后，将以官方 [`with-static-export`](https://github.com/vercel/next.js/tree/canary/examples/with-static-export)示例为模板生成一个新的 `my-app` 目录。请切换到该目录继续。

```sh
$ cd my-app
```

### 创建 GitHub 仓库

访问 [repo.new](https://repo.new) 创建一个新的 GitHub 仓库。创建新仓库后，在终端运行以下命令，准备并向 GitHub 推送本地应用程序：

```sh
$ git remote add origin https://github.com/<GH_USERNAME>/<REPOSITORY_NAME>.git
$ git branch -M main
$ git push -u origin main
```

### 将应用程序部署到 Cloudflare 页面

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，然后在**设置构建和部署**部分，选择 _Next.js (Static HTML Export)_ 作为你的**框架预设**。你的选择将提供以下信息。

{{<pages-build-preset framework="next-js-static">}}

配置好网站后，你就可以开始第一次部署了。Cloudflare Pages 将安装 `next`、项目依赖项，并在部署前构建网站。

## 预览你的网站

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。

每次你向 Next.js 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。