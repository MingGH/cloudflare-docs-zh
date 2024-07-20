---
pcx_content_type: how-to
title: VitePress
---

# VitePress

[VitePress](https://vitepress.dev/)是一款[静态网站生成器](https://en.wikipedia.org/wiki/Static_site_generator) (SSG)，专为快速构建以内容为中心的网站而设计。VitePress 可获取以[Markdown](https://en.wikipedia.org/wiki/Markdown)编写的源内容，为其应用主题，并生成可轻松部署到任何地方的静态 HTML 页面。

在本指南中，你将创建一个新的 VitePress 项目，并使用 Cloudflare Pages 进行部署。

## 建立一个新项目

VitePress 随附一个命令行设置向导，可帮助你构建一个基本项目。

在终端运行以下命令，创建一个新的 VitePress 项目：

{{<tabs labels="npm | pnpm | yarn | bun">}}
{{<tab label="npm" default="true">}}

```sh
$ npx vitepress@latest init
```
{{</tab>}}
{{<tab label="pnpm">}}

```sh
$ pnpm dlx vitepress@latest init
```
{{</tab>}}
{{<tab label="yarn">}}

```sh
$ yarn dlx vitepress@latest init
```
{{</tab>}}
{{<tab label="bun">}}

```sh
$ bunx vitepress@latest init
```
{{</tab>}}
{{</tabs>}}

除其他问题外，设置向导还会询问你将新项目保存在哪个目录中，请确保
目录中，然后使用以下命令安装 `vitepress` 依赖项：

{{<tabs labels="npm | pnpm | yarn | bun">}}
{{<tab label="npm" default="true">}}

```sh
$ npm add -D vitepress
```
{{</tab>}}
{{<tab label="pnpm">}}

```sh
$ pnpm add -D vitepress
```
{{</tab>}}
{{<tab label="yarn">}}

```sh
$ yarn add -D vitepress
```
{{</tab>}}
{{<tab label="bun">}}

```sh
$ bun add -D vitepress
```
{{</tab>}}
{{</tabs>}}

{{<Aside type="note">}}

如果遇到错误，请确保你的本地计算机符合 [VitePress 的先决条件](https://vitepress.dev/guide/getting-started#prerequisites)。

{{</Aside>}}

最后创建一个内容如下的 `.gitignore` 文件：
```
---
filename: .gitignore
---
node_modules
.vitepress
```

这一步可以确保项目的 git 仓库(我们将在下一步进行设置)不会包含不必要的文件。

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，在**设置构建和部署**部分，将提供以下信息：

{{<pages-build-preset framework="vitepress">}}

配置好网站后，你就可以开始首次部署了。Cloudflare Pages 将安装`vitepress`、你的项目依赖项，并在部署前构建你的网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署你的网站后，你将在 `*.pages.dev`上为你的项目获得一个唯一的子域。每次你向 VitePress 项目提交和推送新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还将在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在部署到生产环境之前预览网站的更改效果。

{{<render file="/_framework-guides/_learn-more.md" withParameters="VitePress">}}