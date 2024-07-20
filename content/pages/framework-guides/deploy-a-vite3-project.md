---
pcx_content_type: how-to
title: Vite 3
---

# Vite 3

[Vite](https://vitejs.dev)是面向前端开发人员的下一代构建工具。随着[Vite 3](https://vitejs.dev/blog/announcing-vite3.html)的发布，开发人员可以利用新的命令行(CLI)改进、启动模板和[更多](https://github.com/vitejs/vite/blob/main/packages/vite/CHANGELOG.md#300-2022-07-13)来帮助构建他们的前端应用程序。

Cloudflare Pages 原生支持 Vite 3 项目。有关使用 Vite 3 和 Cloudflare Pages 优化应用程序构建工具的更多信息，请参阅博文[Pages 构建流程的改进](https://blog.cloudflare.com/cloudflare-pages-build-improvements/)，包括亚秒级构建初始化。

在本指南中，你将学习如何使用 Vite 3 启动一个新项目，并将其部署到 Cloudflare Pages。

```sh
$ npm create vite@latest
✔ Project name: … vite-on-pages
✔ Select a framework: › vue
✔ Select a variant: › vue

Scaffolding project in ~/src/vite-on-pages...

Done. Now run:

  cd vite-on-pages
  npm install
  npm run dev
```

现在，你将创建一个新的 GitHub 仓库，并使用 [GitHub 的 `gh` 命令行 (CLI)](https://cli.github.com) 推送你的代码：

```sh
$ git init
Initialized empty Git repository in ~/vite-vue3-on-pages/.git/
$ git add .
$ git commit -m "Initial commit"                                           vite-vue3-on-pages/git/main +
[main (root-commit) dad4177] Initial commit
 14 files changed, 1452 insertions(+)
$ gh repo create
✓ Created repository kristianfreeman/vite-vue3-on-pages on GitHub
✓ Added remote git@github.com:kristianfreeman/vite-vue3-on-pages.git
$ git push
```

将网站部署到Pages：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择新的 GitHub 仓库。
4. 在**设置构建和部署**中，将 `npm run build` 设置为**构建命令**，将 `dist` 设置为**构建输出目录**。

完成配置后，选择**保存并部署**。

你将看到第一个部署管道正在进行中。Pages 会按照指定安装所有依赖项并构建项目。部署项目后，它将在 `<YOUR_PROJECT_NAME>.pages.dev`子域中可用。在**Workers & Pages**> Select your Pages project > **Deployments**中找到项目的子域。

Cloudflare Pages 会自动重建你的项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将项目中的更改部署到生产环境之前，用一个真实的 URL 进行预览。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Vite 3">}}