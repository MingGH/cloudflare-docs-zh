---
pcx_content_type: how-to
title: Astro
---

# Astro

[Astro](https://astro.build)是一个一体化网站框架，用于构建快速、以内容为重点的网站。默认情况下，Astro 可构建零 JavaScript 运行时代码的网站。

请参阅 [Astro Docs](https://docs.astro.build/) 了解有关 Astro 的更多信息，或寻求有关 Astro 项目的帮助。

在本指南中，你将创建一个新的 Astro 应用程序，并使用 Cloudflare Pages 进行部署。

## 建立一个新项目

要使用 `create-cloudflare` 创建新的 Astro 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-astro-app -- --framework=astro
```

Astro 会问：

1. 你想设置哪种项目类型。你的答案不会影响本教程的其他内容。请选择适合你项目的答案。

2. 如果要初始化 Git 仓库。我们建议你选择 `No`，并遵循本指南的 [Git 说明](/pages/framework-guides/deploy-an-astro-site/#create-a-github-repository)。如果选择 `yes`，请不要完全按照下面的 Git 说明进行操作，而是根据自己的需要进行调整。

然后，`create-cloudflare`会安装依赖项，包括[Wrangler](/workers/wrangler/install-and-update/#check-your-wrangler-version) CLI和`@astrojs/cloudflare`适配器，并向你提出设置问题。

### Astro 配置

你可以使用 [`@astrojs/cloudflare` 适配器](https://github.com/withastro/adapters/tree/main/packages/cloudflare#readme) 将 Astro 服务器端渲染 (SSR) 网站部署到 Cloudflare 页面。SSR 网站在页面功能上呈现，允许动态功能和自定义。

{{<render file="_c3-adapter.md">}}

通过运行[`@astrojs/cloudflare` adapter](https://github.com/withastro/adapters/tree/main/packages/cloudflare#readme)，将[`@astrojs/cloudflare`adapter]添加到项目的`package.json`中：

```sh
$ npm run astro add cloudflare
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

{{<render file="_deploy-via-c3.md" withParameters="Astro">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。

如果你尚未授权访问你的 GitHub 帐户，系统将要求你进行授权。Cloudflare 需要这样才能从源代码监控和部署你的项目。如果你愿意，可以缩小对特定版本库的访问范围；但是，当你要向 Cloudflare 页面添加更多版本库时，必须手动更新此列表[在你的 GitHub 设置中](https://github.com/settings/installations)。

选择你创建的新 GitHub 仓库，并在**设置构建和部署**部分提供以下信息：

<div>

{{<pages-build-preset framework="astro">}}

</div>

你可以选择自定义**项目名称**字段。它默认为 GitHub 仓库的名称，但不必与之匹配。项目名称**值将被指定为`*.pages.dev`子域。

完成配置后，选择**保存并部署**。

你将看到第一次部署正在进行中。Pages 会按照指定安装所有依赖项并构建项目。

Cloudflare Pages 会自动重建你的项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将项目中的更改部署到生产环境之前，用一个真实的 URL 进行预览。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

### 本地运行时间

本地运行时支持通过 `platformProxy` 选项进行配置：

```js
---
filename: astro.config.mjs
highlight: [6]
---
import { defineConfig } from "astro/config";
import cloudflare from "@astrojs/cloudflare";

export default defineConfig({
  adapter: cloudflare({
    platformProxy: {
      enabled: true,
    },
  }),
});
```

## 在 Astro 应用程序中使用绑定

[绑定](/pages/functions/bindings/) 允许你的应用程序与 Cloudflare 开发人员产品交互，例如 [KV](/kv/reference/how-kv-works/)、[Durable Object](/durable-objects/)、[R2](/r2/) 和 [D1](https://blog.cloudflare.com/introducing-d1/)。

在 Astro 组件和 API 路由中使用绑定，方法是使用 [Astro Middleware](https://docs.astro.build/en/guides/middleware/) 中的 `context.locals` 访问 Cloudflare 运行时，该运行时除其他字段外，还包含 Cloudflare 的环境以及为你的应用程序连续设置的任何绑定。

请参考以下示例，了解如何使用 TypeScript 访问 KV 命名空间。

首先，你需要通过更新 `env.d.ts` 来定义 Cloudflare 运行时和 KV 类型：

```typescript
---
filename: src/env.d.ts
---
/// <reference types="astro/client" />

type KVNamespace = import("@cloudflare/workers-types").KVNamespace;
type ENV = {
  // replace `MY_KV` with your KV namespace
  MY_KV: KVNamespace;
};

// use a default runtime configuration (advanced mode).
type Runtime = import("@astrojs/cloudflare").Runtime<ENV>;
declare namespace App {
  interface Locals extends Runtime {}
}
```

然后，你可以通过以下方式从 API 端点访问 KV：

```typescript
---
filename: src/pages/my-endpoint.ts
highlight: [3, 4, 5]
---
import type { APIContext } from "astro";

export async function get({locals}: APIContext) => {
  // the type KVNamespace comes from the @cloudflare/workers-types package
  const { MY_KV } = locals.runtime.env;

  return {
    // ...
  };
};
```

除了端点，你还可以直接使用 Astro 组件中的绑定：

```typescript
---
filename: src/pages/index.astro
highlight: [2, 3]
---
---
const myKV = Astro.locals.runtime.env.MY_KV;
const value = await myKV.get("key");
---
<div>{value}</div>
```

要了解有关 Astro Cloudflare 运行时的更多信息，请参阅 Astro 文档中的[访问 Cloudflare 运行时](https://docs.astro.build/en/guides/integrations-guide/cloudflare/#access-to-the-cloudflare-runtime)。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Astro">}}