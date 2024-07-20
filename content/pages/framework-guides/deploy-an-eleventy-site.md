---
pcx_content_type: how-to
title: Eleventy
---

# Eleventy

[Eleventy](https://www.11ty.dev/) 是一个简单的静态网站生成器。在本指南中，你将创建一个新的 Eleventy 站点，并使用 Cloudflare Pages 进行部署。你将使用 `eleventy` CLI 创建一个新的 Eleventy 站点。

## 安装 Eleventy

在终端运行以下命令，安装 `eleventy` CLI：

```sh
$ npm install -g @11ty/eleventy
```

## 创建一个新项目

Eleventy 网站上有很多[启动项目](https://www.11ty.dev/docs/starter/)。举例来说，请在终端运行以下命令，使用 `eleventy-base-blog `项目：

```sh
$ git clone https://github.com/11ty/eleventy-base-blog.git my-blog-name
$ cd my-blog-name
$ npm install
```

{{<render file="_tutorials-before-you-start.md">}}

## 创建 GitHub 仓库

访问 [repo.new](https://repo.new) 创建一个新的 GitHub 仓库。创建新仓库后，在终端运行以下命令，准备并将本地应用程序推送到 GitHub：

```sh
$ git remote set-url origin https://github.com/yourgithubusername/githubrepo
$ git branch -M main
$ git push -u origin main
```

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，在 `**设置构建和部署**`部分，选择 _Eleventy_ 作为你的**框架预设**。你的选择将提供以下信息：

{{<pages-build-preset framework="eleventy">}}

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Eleventy 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Eleventy">}}