---
pcx_content_type: how-to
title: Static HTML
---

# 静态 HTML

Cloudflare 支持将任何静态 HTML 网站部署到 Cloudflare 页面。如果你在管理网站时未使用框架或静态网站生成器，或者你的框架未列在 [框架指南](/pages/framework-guides/) 中，你仍可使用本指南进行部署。

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

<div>

| Configuration option     | Value                 |
| ------------------------ | --------------------- |
| Production branch        | `main`                |
| Build command (optional) | `exit 0`              |
| Build output directory   | `<YOUR_BUILD_DIR>`    |

</div>

与许多框架指南不同，你网站的构建命令和构建输出目录将完全自定义。如果你没有使用预设，也不需要构建网站，请使用 `exit 0` 作为你的**构建命令**。Cloudflare 建议使用 `exit 0` 作为你的**构建命令**，以访问页面功能等功能。**构建输出目录**是你的应用程序内容所在的目录。

配置好网站后，就可以开始第一次部署了。你的自定义构建命令(如果已提供)将会运行，Pages 将部署你的静态网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上收到一个唯一的项目子域。Cloudflare Pages 将自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters=" ">}}