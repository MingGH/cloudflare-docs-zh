---
pcx_content_type: how-to
title: Jekyll
---

# 杰基尔

[Jekyll](https://jekyllrb.com/)是一个用于创建网站的开源框架，以 Markdown 和 Liquid 模板为基础。在本指南中，你将创建一个新的 Jekyll 应用程序，并使用 Cloudflare Pages 进行部署。你将使用 `jekyll` CLI 创建一个新的 Jekyll 网站。

{{<Aside type="note">}}

如果 GitHub Pages 上已有 Jekyll 网站，请参阅[Jekyll 迁移指南](/pages/migrations/migrating-jekyll-from-github-pages/)。

{{</Aside>}}

## 安装 Jekyll

Jekyll 是用 Ruby 编写的，这意味着你需要一个正常运行的 Ruby 安装，如 `rbenv` 来安装 Jekyll。

要在计算机上安装 Ruby，请遵循 [`rbenv`安装说明](https://github.com/rbenv/rbenv#installation)，并在终端中运行 `rbenv` 命令来选择 Ruby 的最新版本。你安装的 Ruby 版本也将用于配置应用程序的页面部署。

```sh
$ rbenv install <RUBY_VERSION> # For example, 3.1.3
```

安装好 Ruby 后，就可以安装 `jekyll` Ruby gem：

```sh
$ gem install jekyll
```

## 创建一个新项目

安装好 Jekyll 后，你可以在终端运行 `jekyll new` 创建一个新项目：

```sh
$ jekyll new my-jekyll-site
```

在新创建的文件夹中创建一个基本的 `index.html`，为网站提供内容：

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Hello from Cloudflare Pages</title>
  </head>
  <body>
    <h1>Hello from Cloudflare Pages</h1>
  </body>
</html>
```

如果你想在新的 Jekyll 网站开始时就有很好的默认样式，也可以选择使用一个主题。例如，[`minimal-mistakes`](https://github.com/mmistakes/minimal-mistakes) 主题有一个[从 `jekyll new` 开始](https://mmistakes.github.io/minimal-mistakes/docs/quick-start-guide/#starting-from-jekyll-new) 部分，可以帮助你把主题添加到新网站。

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository_no_init.md">}}

如果你要将现有的 Jekyll 项目迁移到 Pages，请确认你的 `Gemfile` 已作为代码库的一部分提交。Pages 会查看你的 Gemfile 并运行 `bundle install` 为你的项目安装所需的依赖项，包括 `jekyll` gem。

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

{{<pages-build-preset framework="jekyll">}}

添加与本地使用的 Ruby 版本相匹配的 [环境变量](/pages/configuration/language-support-and-tools/)。在预览版和生产版部署中都将其设置为 `RUBY_VERSION`。下面以 `3.1.3`为例：

| Environment variable | Value          |
| -------------------- | -------------- |
| `RUBY_VERSION`       | `3.1.3`        |

配置好网站后，你就可以开始第一次部署了。你应该会看到 Cloudflare 页面正在安装 `jekyll`、项目依赖项，并在部署前构建你的网站。

{{<Aside type="note">}}

有关将第一个网站部署到 Cloudflare 页面的完整指南，请参阅[入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Jekyll 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Jekyll">}}