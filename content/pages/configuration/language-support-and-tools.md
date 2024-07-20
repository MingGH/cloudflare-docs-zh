---
pcx_content_type: concept
title: 语言支持和工具
layout: language-support-and-tools
rss: https://github.com/cloudflare/cloudflare-docs/commits/production/content/pages/_partials/_platform-language-support-and-tools.atom
outputs:
  - html
  - json
---

# 语言支持和工具

Cloudflare Pages 的构建环境广泛支持各种语言，如 Ruby、Node.js、Python、PHP 和 Go。

如果需要使用某种语言的特定版本(例如 Node.js 或 Ruby)，可以在构建配置中提供相关环境变量，或在源代码中设置相关文件来指定。

## V2 构建系统

2023 年 5 月发布的 [v2 构建系统](https://blog.cloudflare.com/moderizing-cloudflare-pages-builds-toolbox/) 为项目构建带来了多项改进。要迁移到新版本，请在仪表板中配置 Pages 项目设置：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 选择 **工作区和页面**> 在 **概览**中，选择你的页面项目。
3. 转到**设置**> **构建和部署**> **构建系统版本**，然后选择最新版本。

值得注意的是，我们对包含的语言和工具的默认版本进行了更改。要提交有关 v2 的选择、缺失的工具和语言或迁移到 v2 时遇到的问题的反馈，请加入 [Cloudflare Developer Discord](https://discord.com/invite/cloudflaredev)。

如果你之前依赖于构建系统中任何语言或工具的默认版本，则在迁移到 v2 时，你的构建可能会失败。 要解决这个问题，你必须指定你希望使用的版本。有关如何指定每种语言和工具的详细信息，请参见本页。例如，如果你以前依赖于 v1 版构建系统中的 Node.js 默认版本，那么迁移到 v2 版时，你必须通过设置 `NODE_VERSION` 环境变量或在项目中添加 `.node-version` 或 `.nvmrc` 文件来指定你需要 Node.js `12.18.0`。

我们意识到 v2 版构建系统存在一些未解决的问题，我们打算修复这些问题：

- 将 Node.js 版本指定为代号(例如，`hydrogen `或 `lts/hydrogen`)。
- 从 `yarn.lock` 文件版本检测 Yarn 版本。
- 基于 `pnpm-lock.yaml` 文件版本检测 pnpm 版本。
- 从 `package.json` -> `引擎`检测 Node.js 和软件包管理器。
- 支持`pipenv`和`Pipfile`。

## 支持的语言和工具

在下表中，请查看 Cloudflare Pages 构建环境中包含的工具的预安装版本，以及如何覆盖相关版本：

{{<languages>}}

许多常用工具已预装在 Cloudflare Pages 构建环境中。可用于覆盖预安装版本的环境变量在下表中指定(如有)：

{{<tools>}}

如果你想设置 Cloudflare Pages 项目使用的框架的特定版本，请注意 Pages 将在构建过程中尊重你选择的软件包管理器。例如，如果你使用 Gatsby，你的 `package.json` 应指明 `gatsby` npm 软件包的版本，当你的项目在 Cloudflare Pages 上构建时，将使用 `npm install` 安装该软件包。

## 构建环境

Cloudflare 页面构建在 [gVisor](https://gvisor.dev/docs/) 容器中运行。

{{<build-environment>}}