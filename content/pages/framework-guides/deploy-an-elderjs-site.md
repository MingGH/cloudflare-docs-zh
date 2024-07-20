---
pcx_content_type: how-to
title: Elder.js
---

# Elder.js

[Elder.js](https://elderguide.com/tech/elderjs/)是一个专注于搜索引擎优化的框架，用于使用[Svelte](/pages/framework-guides/deploy-a-svelte-site/)构建静态网站。

在本指南中，你将创建一个新的 Elder.js 应用程序，并使用 Cloudflare Pages 进行部署。

## 设置新项目

使用 [`npx degit Elderjs/template`](https://docs.npmjs.com/cli/v6/commands/npm-init)创建一个新项目，并为其命名：

```sh
$ npx degit Elderjs/template elderjs-app
$ cd elderjs-app
```

Elder.js 模板包含大量页面和示例，展示了如何构建静态网站，但只需生成项目，即可将其部署到 Cloudflare 页面。

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。

如果你尚未授权访问你的 GitHub 帐户，系统将要求你进行授权。Cloudflare 需要这样才能从源代码监控和部署你的项目。如果你愿意，可以缩小对特定版本库的访问范围；但是，当你要向 Cloudflare 页面添加更多版本库时，必须手动更新此列表[在你的 GitHub 设置中](https://github.com/settings/installations)。

选择你创建的新 GitHub 仓库，并在**设置构建和部署**部分提供以下信息：

{{<pages-build-preset framework="elder-js">}}

你可以选择自定义**项目名称**字段。它默认为 GitHub 仓库的名称，但不必与之匹配。项目名称**值将被指定为`*.pages.dev`子域。

### 最终设置

完成配置后，单击**保存和部署**按钮。

你将看到第一个部署管道正在进行中。页面会安装所有依赖项，并按指定方式构建项目。

Cloudflare Pages 会自动重建你的项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将项目中的更改部署到生产环境之前，用一个真实的 URL 进行预览。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

{{<render file="/_framework-guides/_learn-more.md" withParameters="Elder.js">}}