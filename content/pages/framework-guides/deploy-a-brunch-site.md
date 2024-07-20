---
pcx_content_type: how-to
title: Brunch
---

# 早午餐

[Brunch](https://brunch.io/)是一款快速的前端网络应用程序构建工具，具有简单的声明式配置和无缝增量编译功能，可用于快速开发。

## 安装早午餐

首先，安装 Brunch：

```sh
$ npm install -g brunch
```

## 创建一个早午餐项目

Brunch 维护着一个社区提供的 [skeletons](https://brunch.io/skeletons) 库，为你的项目提供模板。使用 `brunch new` 命令运行 Brunch 推荐的 `es6` 骨架：

```sh
$ brunch new proj -s es6
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1.登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2.在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3.选择你创建的新 GitHub 仓库，然后在**设置构建和部署**部分，选择*Brunch*作为你的**框架预设**。你的选择将提供以下信息。

{{<pages-build-preset framework="brunch">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。

每次你向 Brunch 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，并在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Brunch">}}