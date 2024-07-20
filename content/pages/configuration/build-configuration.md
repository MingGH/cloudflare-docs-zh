---
pcx_content_type: concept
title: 建立配置
layout: build-configuration
rss: https://github.com/cloudflare/cloudflare-docs/commits/production/content/pages/_partials/_build-configuration.atom
outputs:
  - html
  - json
---
# 建立配置

你可以告诉 Cloudflare Pages 网站的构建方式以及输出文件的位置。

## 构建命令和目录

你应提供一个构建命令，告诉 Cloudflare Pages 如何构建你的应用程序。对于此处未列出的项目，请考虑阅读该工具的文档或框架，并提交拉取请求将其添加到此处。

构建目录表示你的项目构建命令输出 Cloudflare Pages 网站构建版本的位置。通常，该目录默认为行业标准 `public`，但你可能会发现需要对其进行自定义。

{{<details header="Understanding your build configuration">}}

构建命令由框架提供。例如，Gatsby 框架使用 `gatsby build `作为构建命令。在没有框架的情况下工作时，请将**构建命令**字段留空。

联编目录由联编命令生成。每个框架都有自己的命名约定，例如，许多框架的联编输出目录都命名为`/public`。

根目录是网站内容所在的位置。如果未指定，Cloudflare 会假定你链接的 git 仓库为根目录。在 monorepos 等情况下需要指定根目录，因为一个仓库中可能有多个项目。

{{</details>}}

## 框架预设

Cloudflare 维护一个常用框架和工具的构建配置列表。你可以在创建项目时访问这些配置。以下是一些常用框架和工具的标准构建命令和目录。

如果不使用预设，请使用 `exit 0` 作为**编译命令**。

{{<pages-build-presets-table>}}

## 环境变量

如果你的项目使用环境变量来构建网站，请提供自定义环境变量：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 在**概览**中，选择你的页面项目。
4. 选择 **设置**> **环境变量**。

默认情况下会注入以下系统环境变量(但可以重写)：

| 环境变量 | 注入值 | 示例用例 |
| --------------------- | ------------------------------------- | --------------------------------------------------------------------------------------- |
| `CF_PAGES`            | `1`                                   | Changing build behaviour when run on Pages versus locally                               |
| `CF_PAGES_COMMIT_SHA` | `<sha1-hash-of-current-commit>`       | Passing current commit ID to error reporting, for example, Sentry                       |
| `CF_PAGES_BRANCH`     | `<branch-name-of-current-deployment>` | Customizing build based on branch, for example, disabling debug logging on `production` |
| `CF_PAGES_URL`        | `<url-of-current-deployment>`         | Allowing build tools to know the URL the page will be deployed at                       |

## 语言支持和工具

移至 [语言支持和工具](/pages/configuration/language-support-and-tools/)。