---
pcx_content_type: how-to
title: Angular
---

# Angular

[Angular](https://angular.io/)是一个非常流行的框架，可用于构建反应灵敏、功能强大的前端应用程序。

在本指南中，你将创建一个新的 Angular 应用程序，并使用 Cloudflare Pages 进行部署。

## 使用 `create-cloudflare` CLI(C3)创建新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 建立新项目。C3 将创建一个新的项目目录，启动 Angular 的官方设置工具，并提供即时部署选项。

要使用 `create-cloudflare` 创建新的 Angular 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-angular-app -- --framework=angular
```

`create-cloudflare` 将安装依赖项，包括 [Wrangler](/workers/wrangler/install-and-update/#check-your-wrangler-version) CLI 和 Cloudflare 页面适配器，并询问你设置问题。

{{<Aside type="note" header="Git integration">}}

通过 C3 创建的初始部署称为 [Direct Upload](/pages/get-started/direct-upload/)。要通过 Pages Git 集成设置部署，请参阅下面的 [Git 集成](#git-integration) 部分。

{{</Aside>}}


{{<render file="/_framework-guides/_git-integration.md">}}

### 创建 GitHub 仓库

{{<render file="/_framework-guides/_create-gh-repo.md">}}
<br/>

```sh
# Skip the following three commands if you have built your application
# using C3 or already committed your changes
$ git init
$ git add .
$ git commit -m "Initial commit"

$ git branch -M main
$ git remote add origin https://github.com/<YOUR_GH_USERNAME>/<REPOSITORY_NAME>
$ git push -u origin main
```

### 创建一个页面项目

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 转到 **Workers & Pages**> **Create application**> **Pages**> **Connect to Git**并创建一个新的 Pages 项目。

如果你尚未授权访问你的 GitHub 帐户，系统将要求你进行授权。Cloudflare 需要这样才能从源代码监控和部署你的项目。如果你愿意，可以缩小对特定版本库的访问范围；但是，当你要向 Cloudflare 页面添加更多版本库时，必须手动更新此列表[在你的 GitHub 设置中](https://github.com/settings/installations)。

3. 选择创建的新 GitHub 仓库，并在 **Set up builds and deployments** 部分提供以下信息：

{{<pages-build-preset framework="angular">}}

你可以选择自定义**项目名称**字段。它默认为 GitHub 仓库的名称，但不必与之匹配。项目名称**值将被指定为`*.pages.dev`子域。

4. 完成配置后，选择**保存并部署**。

查看正在进行的第一个部署管道。Pages 按照指定安装所有依赖项并构建项目。Cloudflare Pages 会自动重建项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将更改部署到生产环境之前，用一个真实的 URL 来预览对项目所做的更改。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Angular">}}