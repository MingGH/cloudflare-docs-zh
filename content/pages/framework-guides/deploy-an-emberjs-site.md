---
pcx_content_type: how-to
title: Ember
---

# Ember

[Ember.js](https://emberjs.com)是一个富有成效、久经考验的 JavaScript 框架，用于构建现代网络应用程序。它包含构建可在任何设备上运行的丰富用户界面所需的一切。

## 安装 Ember

首先，安装 Ember：

```sh
$ npm install -g ember-cli
```

## 创建一个 Ember 项目

使用 `ember new` 命令创建新应用程序：

```sh
$ npx ember new ember-quickstart --lang en
```

应用程序生成后，将目录更改为项目，然后运行以下命令运行项目：

```sh
$ cd ember-quickstart
$ npm start
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository_no_init.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，并在  **Set up builds and deployments**部分选择 _Ember_ 作为你的**框架预设**。你的选择将提供以下信息：

{{<pages-build-preset framework="ember-js">}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。

每次你向 Ember 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，并在部署到生产环境之前预览网站的更改效果。

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Ember">}}