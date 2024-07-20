---
pcx_content_type: how-to
title: Hugo
---

# Hugo

[Hugo](https://gohugo.io/)是一款用 Go 语言编写的静态网站生成工具。它的运行速度快得令人难以置信，而且拥有强大、灵活的高级原语，可使用不同的[内容格式](https://gohugo.io/content-management/formats/)管理内容。

在本指南中，你将创建一个新的 Hugo 应用程序，并使用 Cloudflare Pages 进行部署。你将使用 `hugo` CLI 创建一个新的 Hugo 站点。

{{<render file="_tutorials-before-you-start.md">}}

如果你已经有一个托管在 [Git 提供商](/pages/get-started/git-integration/)的 Hugo 网站，请转到 [使用 Cloudflare 页面部署](#deploy-with-cloudflare-pages)。

## Hugo安装

使用操作系统的具体说明安装 Hugo CLI。

{{<tabs labels="MacOS | Windows | Linux">}}
{{<tab label="macos" default="true">}}

如果使用软件包管理器 [Homebrew](https://brew.sh)，请在终端运行 `brew install` 命令安装 Hugo：

```sh
$ brew install hugo
```

{{</tab>}}
{{<tab label="windows">}}

如果使用软件包管理器 [Chocolatey](https://chocolatey.org/)，请在终端运行 `choco install` 命令安装Hugo：

```sh
$ choco install hugo --confirm
```

如果使用软件包管理器 [Scoop](https://scoop.sh/)，请在终端运行 `scoop install` 命令安装 Hugo：

```sh
$ scoop install hugo
```

{{</tab>}}
{{<tab label="linux">}}

你的 Linux 发行版的软件包管理器可能包含 Hugo。如果是这种情况，请使用发行版的软件包管理器直接安装 Hugo，例如，在 Ubuntu 中，运行以下命令：

```sh
$ sudo apt-get install hugo
```

如果你的软件包管理器不包含 Hugo，或者你想直接下载发行版，请参阅 [**手册**](/pages/framework-guides/deploy-a-hugo-site/#manual-installation)部分。
{{</tab>}}
{{</tabs>}}

### 手动安装

Hugo GitHub 仓库包含适用于各种操作系统的 Hugo 命令行工具预构建版本，可在 [Releases 页面](https://github.com/gohugoio/hugo/releases) 上找到。

有关安装这些版本的更多说明，请参阅 [Hugo 文档](https://gohugo.io/getting-started/installing/)。

## 创建一个新项目

安装 Hugo 后，请参阅 [Hugo 快速入门](https://gohugo.io/getting-started/quick-start/) 创建项目，或在终端运行 `hugo new` 命令创建新项目：

```sh
$ hugo new site my-hugo-site
```

Hugo网站使用主题来定制静态构建的 HTML 网站的外观和感觉。[theme.gohugo.io](https://themes.gohugo.io)提供了许多主题，目前请在终端运行以下命令，使用[Ananake 主题](https://themes.gohugo.io/themes/gohugo-theme-ananke/)：

```sh
$ cd my-hugo-site
$ git init
$ git submodule add https://github.com/theNewDynamic/gohugo-theme-ananke.git themes/ananke
$ echo "theme = 'ananke'" >> hugo.toml
```

## 创建帖子

创建一篇新文章，为Hugo网站添加一些初始内容。在终端运行 "hugo new "命令，生成新帖子：

```sh
$ hugo new content posts/hello-world.md
```

在 `hello-world.md` 中添加一些初始内容以创建帖子。准备发布文章时，移除文章 frontmatter 中的 `draft` 行。任何设置了 `draft: true` 的文章都会被Hugo的构建过程跳过。

{{<render file="/_framework-guides/_create-github-repository_no_init.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **Workers & Pages** > **创建应用程序** > **页面** > **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 **设置构建和部署**部分提供以下信息：

{{<pages-build-preset framework="hugo">}}

{{<Aside type="note" header="Base URL configuration">}}

Hugo 允许你配置应用程序的 `baseURL` 。这样，你就可以使用 `absURL` 辅助程序来构建完整的规范 URL。要在 Pages 中做到这一点，你必须为你的 `hugo` 编译命令提供 `-b` 或 `--baseURL` 标志和 `CF_PAGES_URL` 环境变量。

你的最终构建命令可能是这样的

```sh
$ hugo -b $CF_PAGES_URL
```

{{</Aside>}}

完成部署配置后，选择**保存并部署**。在部署之前，你应该会看到 Cloudflare 页面正在安装 `hugo` 和你的项目依赖项，并正在构建你的网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev` 上收到一个唯一的项目子域。
每次你向 Hugo 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

## 使用特定或更新的Hugo版本

要使用[特定或更新版本的 Hugo](https://github.com/gohugoio/hugo/releases)，请在 Pages 项目 > **设置** > **环境变量**中创建`HUGO_VERSION`环境变量。将值设为你要指定的 Hugo 版本(建议使用 v0.112.0 或更高版本)。

例如，`HUGO_VERSION`: `0.115.4`。

{{<Aside type="note">}}

如果计划使用 [预览部署](/pages/configuration/preview-deployments/)，请确保在 **Preview** 环境中也添加了环境变量。

{{</Aside>}}

{{<render file="/_framework-guides/_learn-more.md" withParameters="Hugo">}}