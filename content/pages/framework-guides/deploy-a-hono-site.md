---
pcx_content_type: how-to
title: Hono
tags: [Hono]
---

# Hono

[Hono](https://honojs.dev/)是一个小型、简单、超快的网络框架，适用于 Cloudflare Pages 和 Workers、Deno 和 Bun。在本指南中，你将创建一个新的 Hono 应用程序，并使用 Cloudflare Pages 进行部署。

## 创建一个新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 创建新项目。C3 将创建一个新的项目目录，启动 Hono 的官方设置工具，并提供即时部署选项。

要使用 `create-cloudflare` 创建一个新的 Hono 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-hono-app -- --framework=hono
```

打开项目并创建一个 `src/server.js` 文件(如果使用的是 TypeScript，则为 `src/server.ts`)。在文件中添加以下内容：

```javascript
import { Hono } from "hono";
const app = new Hono();

app.get("/", (ctx) => ctx.text("Hello world, this is Hono!!"));

export default app;
```

要提供 CSS、图像或 JavaScript 文件等静态文件，请在 `src/server.js/ts` 文件中添加以下内容：

```javascript
app.get("/public/*", async (ctx) => {
  return await ctx.env.ASSETS.fetch(ctx.req.raw);
});
```

这将导致在应用程序中提供 `dist` 中 `public` 文件夹中的所有文件。

{{<Aside type="note">}}

在捆绑过程中会创建并使用 `dist` 目录。你需要在 `dist` 目录中创建一个 `public` 目录。通常不希望在`dist`目录下创建`public`目录，因为`dist`目录不能提交到版本库，而`public`目录可以。

要解决这个问题，有不同的办法。例如，你可以配置 `.gitignore` 文件，使其包含 `dist` 目录，但忽略除 `public` 目录之外的所有上下文。或者，你也可以在其他地方创建一个 `public` 目录，并将其复制到 `dist` 目录中，作为捆绑过程的一部分。

{{</Aside>}}

打开 `package.json` 文件，更新 `scripts` 部分：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}

```json
---
filename: package.json
---
    "scripts": {
        "dev": "run-p dev:*",
        "dev:wrangler": "wrangler pages dev dist --live-reload",
        "dev:esbuild": "esbuild --bundle src/server.js --format=esm --watch --outfile=dist/_worker.js",
        "build": "esbuild --bundle src/server.js --format=esm --outfile=dist/_worker.js",
        "deploy": "wrangler pages publish dist"
    },
```

{{</tab>}}
{{<tab label="ts">}}

```json
---
filename: package.json
---
    "scripts": {
        "dev": "run-p dev:*",
        "dev:wrangler": "wrangler pages dev dist --live-reload",
        "dev:esbuild": "esbuild --bundle src/server.ts --format=esm --watch --outfile=dist/_worker.js",
        "build": "esbuild --bundle src/server.ts --format=esm --outfile=dist/_worker.js",
        "deploy": "wrangler pages publish dist"
    },
```

{{</tab>}}
{{</tabs>}}

然后，运行以下命令。

```sh
$ npm install npm-run-all --save-dev
```

安装 `npm-run-all` 后，你就可以使用一条命令(`npm run dev`)在观察模式下同时运行 `npm run dev:wrangler` 和 `npm run dev:esbuild`。

## 在本地开发环境中运行

运行

```shell
$ npm run dev
```

你应该可以在 `http://localhost:8788` 查看生成的网络应用程序。

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

{{<render file="_deploy-via-c3.md" withParameters="Hono">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

<div>

| Configuration option | Value           |
| -------------------- | --------------- |
| Production branch    | `main`          |
| Build command        | `npm run build` |
| Build directory      | `dist`          |

</div>

配置好网站后，你就可以开始首次部署了。你应该会看到 Cloudflare 页面正在安装 `my-hono-app`、你的项目依赖项，并在部署前构建你的网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Hono 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

## 相关教程

有关 Hono 的更多教程，请参考以下资源：

{{<resource-by-tag tags="Hono" resource_type="tutorial" show_description=false >}}