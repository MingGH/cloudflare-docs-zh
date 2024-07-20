---
title: Git 集成
pcx_content_type: get-started
meta:
  title: Git integration guide
  description: Connect your Git provider to Pages.
---

# Git 整合指南

在本指南中，你将开始使用 Cloudflare Pages，并通过 Git 集成将你的第一个网站部署到 Pages 平台。

## 将 Git 提供商连接到页面

Pages 支持 [GitHub](https://github.com/) 和 [GitLab](https://gitlab.com/)。创建你的第一个 Pages 项目：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在 `账户主页 `中，选择 `**工作者和页面**`。
3. 选择 **创建应用程序**> **页面**> **连接到 Git**。

系统将提示你使用首选的 Git 提供商登录。这将允许 Cloudflare Pages 部署你的项目，并通过 [预览部署](/pages/configuration/preview-deployments/) 更新你的 PR。

{{<Aside type="note">}}

使用 GitLab 登录将授予 Pages 对你账户上所有版本库的访问权限。此外，如果你是 Cloudflare 多用户账户的成员，并使用 GitLab 登录，其他成员也可以将你的仓库部署到 Pages。

如果你使用的是 GitLab，你必须在版本库中拥有 **Maintainer**或更高的角色，才能成功部署 Cloudflare Pages。

{{</Aside>}}

### 选择你的 GitHub 仓库

你可以从个人账户或已授予 Pages 访问权限的组织中选择一个 GitHub 项目。这样就可以选择一个 GitHub 仓库来使用 Pages 进行部署。私有和公共仓库都支持。

### 选择你的 GitLab 仓库

如果使用 GitLab，可以从个人账户或所属的 GitLab 组中选择一个项目。这样就可以选择一个 GitLab 仓库来使用 Pages 进行部署。私人和公共版本库均受支持。

## 配置你的部署

选择 Git 仓库后，选择**安装与授权**和**开始设置**。然后，你可以在**设置构建和部署**中自定义部署。

你的**项目名称**将用于生成项目的主机名。默认情况下，该名称与 Git 项目名称一致。

**生产分支**表示 Cloudflare Pages 部署网站生产版本时应使用的分支。对于大多数项目，这是 `main` 或 `master` 分支。所有非生产分支的其他分支将用于 [预览部署](/pages/configuration/preview-deployments/)。

{{<Aside type="note">}}

你必须至少向 GitHub 或 GitLab 项目推送了一个分支，才能从下拉菜单中选择**生产分支**。

{{</Aside>}}

设置构建和部署页面，填写项目名称和生产分支](/images/pages/get-started/configuration.png)

### 配置你的构建设置

根据你要部署到 Cloudflare Pages 的框架、工具或项目，你需要指定网站的**构建命令**和**构建输出目录**，以告诉 Cloudflare Pages 如何部署你的网站。此目录中的内容将作为你的网站内容上传到 Cloudflare Pages。

{{<Aside type="warning" header="No framework required">}}

部署 Cloudflare Pages 不需要框架。你可以不选择框架，继续阅读入门指南，并参阅[部署你的网站](/pages/framework-guides/deploy-anything/)，了解有关在没有框架的情况下部署网站的更多信息。

{{</Aside>}}

仪表板提供了许多特定于框架的预设。这些预设提供了所选框架的默认构建命令和构建输出目录值。如果不确定本部分的正确值，请参阅 [构建配置](/pages/configuration/build-configuration/)。如果不需要构建步骤，请将**构建命令**字段留空。

![需要填写的构建设置字段](/images/pages/get-started/build-settings.png)

Cloudflare Pages 从版本库的根目录开始工作。整个构建管道，包括安装步骤，都将从该位置开始。如果你想更改，请通过**根目录(高级)**> **路径**字段指定新的根目录位置。

![需要填写的根目录字段](/images/pages/get-started/root-directory.png)

{{<details header="Understanding your build configuration">}}

构建命令由框架提供。例如，Gatsby 框架使用 `gatsby build `作为构建命令。在没有框架的情况下工作时，请将**构建命令**字段留空。

构建命令会生成构建输出目录。每个[框架](/pages/configuration/build-configuration/#framework-presets)都有自己的命名约定，例如，许多框架的联编输出目录都命名为`/public`。

根目录是网站内容所在的位置。如果未指定，Cloudflare 会假定你链接的 Git 仓库为根目录。在 monorepos 等情况下，一个版本库中可能有多个项目，因此需要指定根目录。

更多信息请参阅 [构建配置](/pages/configuration/build-configuration/)。

{{</details>}}

### 环境变量

环境变量是为构建工作流程提供配置的常用方法。在设置项目时，你可以指定一些键值对作为环境变量。项目首次构建完成后，还可以对这些变量进行进一步自定义。

有关如何设置 Node.js 版本环境变量的详细信息，请参阅 [Hexo 框架指南](/pages/framework-guides/deploy-a-hexo-site/#using-a-specific-nodejs-version)。

选择_框架预设_后，如果没有框架，则将该字段留空；配置**根目录(高级)**，并自定义**环境变量(可选)**后，就可以部署了。

## Your first deploy

完成构建配置设置后，选择**保存并部署**。当 Cloudflare 页面安装项目依赖项、构建项目并将其部署到 Cloudflare 全球网络时，你的项目构建日志将输出。

![Cloudflare仪表板中的部署详情](/images/pages/get-started/deploy-log.png)

项目部署完成后，你将收到一个唯一的 URL，用于查看已部署的网站。

{{<Aside type="warning" header="DNS errors">}}

如果在首次部署后访问网站时遇到 DNS 错误，这可能是因为 DNS 还没有来得及传播。要解决该错误，请等待 DNS 传播，或尝试其他设备或网络来解决该错误。

{{</Aside>}}

## 管理你的网站

首次部署后，选择**继续项目**，即可在 Cloudflare Pages 控制面板中查看项目配置。在此页面上，你可以看到项目的当前部署状态、生产 URL 和相关提交以及所有过去的部署。

![网站仪表板显示你的环境和部署](/images/pages/get-started/site-dashboard.png)

### 删除项目

删除页面项目

1. 返回**账户主页**或使用仪表板顶部的下拉菜单。
2. 选择 **工作者和页面**。
3. 选择页面项目 > **设置**> **删除项目**。

{{<Aside type="warning">}}

对于使用自定义域的项目，必须首先删除与 Pages 项目相关的 CNAME 记录。否则可能会使 DNS 记录处于活动状态，导致你的域名指向一个已不存在的 Pages 项目。有关说明，请参阅 [删除自定义域](/pages/configuration/custom-domains/#delete-a-custom-domain)。

对于没有自定义域的项目(任何位于 `*.pages.dev` 子域上的项目)，可以在项目设置中删除项目。

{{</Aside>}}

## 高级项目设置

在**设置**部分，你可以配置高级设置，例如更改项目名称、更新 Git 配置或更新构建命令、构建目录或环境变量。

## 相关资源

- 为你的 Pages 项目设置一个 [自定义域](/pages/configuration/custom-domains/)。
- 启用 [Cloudflare 网站分析](/pages/how-to/web-analytics/)。
- 设置访问策略以 [管理谁可以查看部署预览](/pages/configuration/preview-deployments/#customize-preview-deployments-access)。