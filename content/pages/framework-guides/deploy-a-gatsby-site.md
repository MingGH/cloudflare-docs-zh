---
pcx_content_type: how-to
title: Gatsby
---

# Gatsby

[Gatsby](https://www.gatsbyjs.com/)是一个用于创建网站和应用程序的开源 React 框架。在本指南中，你将创建一个新的 Gatsby 应用程序，并使用 Cloudflare Pages 进行部署。你将使用 `gatsby` CLI 创建一个新的 Gatsby 站点。

## 安装Gatsby

在终端运行以下命令，安装 `gatsby` CLI：

```sh
$ npm install -g gatsby-cli
```

## 创建一个新项目

安装了 Gatsby 后，你可以使用 `gatsby new` 创建一个新项目。`new `命令接受一个 GitHub URL 以使用现有模板。例如，在终端运行以下命令，使用 `gatsby-starter-lumen` 模板。你可以在 [Gatsby 的启动器部分](https://www.gatsbyjs.com/starters/?v=2) 找到更多信息：

```sh
$ npx gatsby new my-gatsby-site https://github.com/alxshelepenok/gatsby-starter-lumen
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository_no_init.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

{{<pages-build-preset framework="gatsby">}}

配置好网站后，你就可以开始第一次部署了。在部署之前，你应该会看到 Cloudflare 页面正在安装 `gatsby`、项目依赖项并构建网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Gatsby 站点提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

## 动态路由

如果在 Gatsby 项目中使用 [dynamic routes](https://www.gatsbyjs.com/docs/reference/functions/routing/#dynamic-routing)，请设置 [proxy redirect](/pages/configuration/redirects/#proxying)，以使这些路由生效。

如果你有动态路由，如 `/users/[id]`，请参照以下示例创建代理重定向：

```
---
filename: _redirects
---
/users/* /users/:id 200
```

{{<render file="/_framework-guides/_learn-more.md" withParameters="Gatsby">}}