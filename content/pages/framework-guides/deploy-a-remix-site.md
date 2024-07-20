---
pcx_content_type: how-to
title: Remix
---

# Remix

[Remix](https://remix.run/)是一个专注于充分利用网络力量的框架。与 Cloudflare Workers 一样，它也使用现代 JavaScript API，并注重网络基本要素，例如有意义的 HTTP 状态代码、缓存以及可用性和性能优化。

在本指南中，你将创建一个新的 Remix 应用程序并部署到 Cloudflare 页面。

## 设置新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 建立新项目。C3 会创建一个新的项目目录，启动 Remix 的官方设置工具，并提供即时部署选项。

要使用 `create-cloudflare` 创建新 Remix 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-remix-app -- --framework=remix
```

`create-cloudflare`将安装其他依赖项，包括[Wrangler](/workers/wrangler/install-and-update/#check-your-wrangler-version) CLI 和任何必要的适配器，并向你提出设置问题。

{{<Aside type="warning" header="Before you deploy">}}
Remix 项目将包含一个 `functions/[[path]].ts` 文件。文件名`[[path]]`表示该文件将处理对所有传入 URL 的请求。请参阅 [Path segments](/pages/functions/routing/#dynamic-routes)，了解更多信息。

如果在运行 `remix vite:build` 之前尝试部署网站，则 `functions/[[path]].ts` 将无法按预期运行。
{{</Aside>}}

设置好项目后，运行以下命令更改目录并渲染项目：

```sh
# choose Cloudflare Pages
$ cd my-remix-app
$ npm run dev
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository_no_init.md">}}

## 使用 Cloudflare 页面部署

{{<render file="_deploy-via-c3.md" withParameters="Remix">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

{{<pages-build-preset framework="remix">}}

配置好网站后，你就可以开始第一次部署了。你应该会看到 Cloudflare 页面正在安装 `npm`、你的项目依赖项，并在部署前构建你的网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Remix 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在部署到生产环境之前预览网站的变更效果。

### 通过 Wrangler CLI 部署

如果使用 [`create-cloudflare`(C3)](https://www.npmjs.com/package/create-cloudflare)创建新 Remix 项目，C3 会自动使用 [`wrangler`](/workers/wrangler/)为项目搭建脚手架。要部署项目，请运行以下命令：

```sh
$ npm run deploy
```

## 为 Remix 应用程序创建并添加绑定

要在 Remix 应用程序中添加绑定，请参阅 [绑定](/pages/functions/bindings/)。
[绑定](/pages/functions/bindings/) 允许你的应用程序与 Cloudflare 开发人员产品交互，例如 [KV 命名空间](/kv/reference/how-kv-works/)、[耐用对象](/durable-objects/)、[R2 存储桶](/r2/) 和 [D1 数据库](/d1/)。

### 在本地开发中绑定资源

Remix 使用 Wrangler 的 [`getPlatformProxy`](/workers/wrangler/api/#getplatformproxy)在本地模拟 Cloudflare 环境。你可通过[`cloudflareDevProxyVitePlugin`](https://remix.run/docs/en/main/future/vite#cloudflare-proxy)在项目的`vite.config.ts`文件中配置`getPlatformProxy`。

要在本地开发中绑定资源，需要在 `wrangler.toml` 文件中配置绑定。请参阅 [绑定](/workers/wrangler/configuration/#bindings)，了解更多信息。

在`wrangler.toml`文件中配置绑定后，代理就可以在`context.cloudflare`中的`loader`或`action`函数中使用：

```typescript
export const loader = ({ context }: LoaderFunctionArgs) => {
  const { env, cf, ctx } = context.cloudflare;
  env.MY_BINDING // Access bound resources here
  // ... more loader code here...
};
```

{{<Aside header="Correcting the env type">}}
你可能已经注意到，在 `wrangler.toml` 中添加附加绑定时，`context.cloudflare.env` 的键入不正确。

要解决这个问题，请运行 `npm run typegen` 生成缺失的类型。这将更新 `worker-configuration.d.ts` 中定义的 `Env` 接口。
运行该命令后，如上图所示，你可以使用 `context.cloudflare.env` 访问 `loader` 或 `action` 中的绑定。
{{</Aside>}}


### 在生产中绑定资源

要在生产中绑定资源，你需要在 Cloudflare 面板中配置绑定。请参阅 [绑定](/pages/functions/bindings/) 文档了解更多信息。

在 Cloudflare 控制面板中配置绑定后，代理就可以在`loader`或`action`函数中的`context.cloudflare.env`中使用，[如上所示](#binding-resources-in-local-development)。

## 示例在 Remix 应用程序中访问 D1 数据库

例如，你将在 Remix 应用程序中绑定并查询 D1 数据库。

1. 创建 D1 数据库。请参阅 [D1 文档](/d1/) 了解更多信息。
2. 在 `wrangler.toml` 文件中为 D1 数据库配置绑定：

```toml
[[ d1_databases ]]
binding = "DB"
database_name = "<YOUR_DATABASE_NAME>"
database_id = "<YOUR_DATABASE_ID>"
```
3. 运行 `npm run typegen` 为绑定生成 TypeScript 类型。

```sh
$ npm run typegen
> typegen
> wrangler types

 ⛅️ wrangler 3.48.0
-------------------
interface Env {
	DB: D1Database;
}
```

4. 在`loader`函数中访问 D1 数据库：

```typescript
---
filename: app/routes/products/$productId.tsx
---
import type { LoaderFunction } from "@remix-run/cloudflare";
import { json } from "@remix-run/cloudflare";
import { useLoaderData } from "@remix-run/react";

export const loader: LoaderFunction = async ({ context, params }) => {
  const { env, cf, ctx } = context.cloudflare;
  let { results } = await env.DB.prepare(
    "SELECT * FROM products where id = ?1"
  ).bind(params.productId).all();
  return json(results);
};

export default function Index() {
  const results = useLoaderData<typeof loader>();
  return (
    <div>
      <h1>Welcome to Remix</h1>
      <div>
        A value from D1:
        <pre>{JSON.stringify(results)}</pre>
      </div>
    </div>
  );
}
```

{{<render file="/_framework-guides/_learn-more.md" withParameters="Remix">}}