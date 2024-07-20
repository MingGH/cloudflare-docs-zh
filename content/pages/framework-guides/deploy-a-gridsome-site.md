---
pcx_content_type: how-to
title: Gridsome
---

# Gridsome

[Gridsome](https://gridsome.org)是一个由 Vue.js 支持的 Jamstack 框架，用于构建默认速度极快的静态生成网站和应用程序。在本指南中，你将创建一个新的 Gridsome 项目，并使用 Cloudflare Pages 进行部署。你将使用 [`@gridsome/cli`](https://github.com/gridsome/gridsome/tree/master/packages/cli)，这是一个用于创建新 Gridsome 项目的命令行工具。

## 安装 Gridsome

在终端运行以下命令，安装 `@gridsome/cli`：

```sh
$ npm install --global @gridsome/cli
```

## 建立一个新项目

安装 Gridsome 后，运行 `gridsome create` 命令创建一个新项目。`create`命令接受一个定义所创建项目目录的名称，以及一个可选的启动工具包名称。你可以在 [Gridsome 启动程序部分](https://gridsome.org/docs/starters/) 查看更多启动程序。

```sh
$ npx gridsome create my-gridsome-website
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，在**设置构建和部署**部分，将提供以下信息：

{{<pages-build-preset framework="gridsome">}}

配置网站后，你可以开始首次部署。在部署之前，你应该看到 Cloudflare 页面正在安装 `vuepress`、项目依赖项并构建网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目获得一个唯一的子域。每次你向 Gridsome 项目提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将网站更改部署到生产环境之前预览其外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Gridsome">}}