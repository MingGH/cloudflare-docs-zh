---
pcx_content_type: how-to
title: Solid
---

# Solid

[Solid](https://www.solidjs.com/)是一个开源网络应用程序框架，专注于基于 JSX 生成具有现代开发人员体验的高性能应用程序。

在本指南中，你将通过 [SolidStart](https://start.solidjs.com/getting-started/what-is-solidstart)(Solid 的元框架)创建一个新的 Solid 应用程序，并使用 Cloudflare Pages 进行部署。

{{<Aside type="warning" header="Important">}}

在撰写本报告时，SolidStart 尚处于测试阶段，因此，如果发生重大变化，以下指南可能会不准确。

_注意：本指南基于 [SolidStart Beta 2 版本](https://github.com/solidjs/solid-start/discussions/1052)_

{{</Aside>}}

## 创建一个新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 来建立一个新项目。C3 将创建一个新项目目录，启动 Solid 的官方设置工具，并提供即时部署选项。

要使用 `create-cloudflare` 创建一个新的 Solid 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-solid-app -- --framework=solid
```

系统将提示你选择启动器。选择任何可用选项。然后会询问你是否要启用服务器端渲染。回复 `是`。最后，系统会询问你是否要使用 TypeScript，请选择 `yes`或 `no`。

然后，`create-cloudflare` 将安装依赖项，包括[Wrangler](/workers/wrangler/install-and-update/#check-your-wrangler-version) CLI 和 SolidStart Cloudflare 页面适配器，并询问你设置问题。

安装完项目依赖项后，启动应用程序：

```sh
$ npm run dev
```

## SolidStart Cloudflare 配置

{{<render file="_c3-adapter.md">}}

为了配置 SolidStart，使其能部署到 Cloudflare 页面，请按如下方式更新其配置文件：

```diff
---
filename: vite.config.(ts|js)
---
import { defineConfig } from "@solidjs/start/config";

export default defineConfig({
+  server: {
+    preset: "cloudflare-pages",

+    rollupConfig: {
+      external: ["node:async_hooks"]
+    }
+  }
});
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare Pages部署

{{<render file="_deploy-via-c3.md" withParameters="Solid">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在**设置构建和部署**中提供以下信息：

<div>

| Configuration option  | Value              |
| --------------------- | ------------------ |
| Production branch     | `main`             |
| Build command         | `npm run build`    |
| Build directory       | `dist`             |

</div>

配置好网站后，你就可以开始第一次部署了。你应该会看到 Cloudflare 页面正在安装 `npm`、你的项目依赖项，并在部署前构建你的网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Solid 代码库提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还将在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，以便在将更改部署到生产之前预览你的网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Solid">}}