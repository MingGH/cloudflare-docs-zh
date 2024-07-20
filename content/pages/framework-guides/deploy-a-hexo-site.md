---
pcx_content_type: how-to
title: Hexo
---

# Hexo

[Hexo](https://hexo.io/)是一款由 Node.js 支持的静态网站生成工具。Hexo 的优点包括快速、简单和灵活，可通过 Node.js 将 Markdown 文件渲染为静态网页。

在本指南中，你将创建一个新的 Hexo 应用程序，并使用 Cloudflare Pages 进行部署。你将使用 `hexo` CLI 创建一个新的 Hexo 站点。

## 安装 Hexo

首先，使用 `npm` 或 `yarn` 安装 Hexo CLI，在终端运行以下任一命令：

```sh
$ npm install hexo-cli -g
# or
$ yarn global add hexo-cli
```

在 macOS 和 Linux 上，可以使用 [brew](https://brew.sh/) 安装：

```sh
$ brew install hexo
```

{{<render file="_tutorials-before-you-start.md">}}

## 创建一个新项目

安装 Hexo CLI 后，在终端运行 `hexo init` 命令创建一个新项目：

```sh
$ hexo init my-hexo-site
$ cd my-hexo-site
$ npm install
```

Hexo 网站使用主题来定制静态构建的 HTML 网站的外观。Hexo 已自动安装了默认主题，你可以在 [Hexo 的主题页面](https://hexo.io/themes/) 找到该主题。

## 创建帖子

创建一篇新文章，为你的 Hexo 网站添加一些初始内容。在终端运行 `hexo new` 命令，生成新的帖子：

```sh
$ hexo new "hello hexo"
```

在 `hello-hexo.md` 中，使用 Markdown 编写文章内容。你可以自定义文章中的标签、类别或其他变量。更多信息请参阅[Hexo 文档](https://hexo.io/docs/) 的[前言部分](https://hexo.io/docs/front-matter)。

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

<div>

| Configuration option | Value           |
| -------------------- | --------------- |
| Production branch    | `main`          |
| Build command        | `hexo generate` |
| Build directory      | `public`        |

</div>

完成配置后，单击**保存并部署**按钮。在部署之前，你应该会看到 Cloudflare 页面正在安装 `hexo` 和你的项目依赖项，并正在构建你的网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Hexo 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

## 使用特定的 Node.js 版本

某些 Hexo 主题或插件对不同的 Node.js 版本有额外要求。要在 Hexo 中使用特定的 Node.js 版本，请参阅下文：

1. 进入 Pages 项目。
2. 进入**设置**> **环境变量**。
3. 设置环境变量 `NODE_VERSION` 和所需的 Node.js 版本值(如 `14.3`)。

![按照上述说明在页面控制面板中设置环境变量](/images/pages/framework-guides/node-version-pages.png)

{{<render file="/_framework-guides/_learn-more.md" withParameters="Hexo">}}