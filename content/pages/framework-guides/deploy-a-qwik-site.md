---
pcx_content_type: how-to
title: Qwik
---

# Qwik

[Qwik](https://github.com/builderio/qwik)是一个开源的、以 DOM 为中心的、可续写的网络应用程序框架，其设计重点是[可续写性](https://qwik.builder.io/docs/concepts/resumable/)、HTML 的服务器端渲染和代码的[细粒度懒加载](https://qwik.builder.io/docs/concepts/progressive/#lazy-loading)，从而尽可能缩短交互时间。

在本指南中，你将通过 [Qwik City](https://qwik.builder.io/qwikcity/overview/)(Qwik 的元框架)创建一个新的 Qwik 应用程序，并使用 Cloudflare Pages 进行部署。

## 创建一个新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 创建新项目。C3 将创建一个新的项目目录，启动 Qwik 的官方设置工具，并提供即时部署选项。

要使用 `create-cloudflare` 创建新的 Qwik 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-qwik-app -- --framework=qwik
```

`create-cloudflare`将安装其他依赖项，包括[Wrangler CLI](/workers/wrangler/install-and-update/#check-your-wrangler-version)和任何必要的适配器，并向你提出设置问题。

作为 `cloudflare-pages `适配器安装的一部分，将创建一个 `functions/[[path]].ts `文件。文件名`[[path]]`表示该文件将处理对所有传入 URL 的请求。请参阅 [Path segments](/pages/functions/routing/#dynamic-routes)，了解更多信息。

选择服务器选项后，将目录更改为项目，然后运行以下命令渲染项目：

```sh
$ npm start
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare Pages部署

{{<render file="_deploy-via-c3.md" withParameters="Qwik">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

{{<pages-build-preset framework="qwik">}}

配置好网站后，你就可以开始第一次部署了。你应该会看到 Cloudflare 页面正在安装 `npm`、你的项目依赖项，并在部署前构建你的网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Qwik 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，以便在将更改部署到生产环境之前预览网站的外观。

## 在 Qwik 应用程序中使用绑定

[绑定](/pages/functions/bindings/) 允许你的应用程序与 Cloudflare 开发人员产品交互，例如 [KV](/kv/reference/how-kv-works/)、[Durable Object](/durable-objects/)、[R2](/r2/) 和 [D1](https://blog.cloudflare.com/introducing-d1/)。

在 QwikCity 中，通过 [routeLoaders](https://qwik.builder.io/qwikcity/route-loader/) 和 [actions](https://qwik.builder.io/qwikcity/action/) 添加服务器端代码。然后通过框架提供的 `platform` 对象访问为应用程序设置的绑定。

下面的代码块展示了在 QwikCity 中访问 KV 命名空间的示例。

```typescript
---
filename: src/routes/index.tsx
highlight: [4, 5]
---
// ...

export const useGetServerTime = routeLoader$(({ platform }) => {
  // the type `KVNamespace` comes from the @cloudflare/workers-types package
  const { MY_KV } = (platform.env as { MY_KV: KVNamespace }));

  return {
    // ....
  }
});
```

{{<render file="/_framework-guides/_learn-more.md" withParameters="Qwik">}}