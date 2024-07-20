---
pcx_content_type: how-to
title: Nuxt
meta:
  description: Web framework making Vue.js-based development simple and powerful.
---

# Nuxt

[Nuxt](https://nuxt.com)是一个网络框架，使基于 Vue.js 的开发变得简单而强大。

在本指南中，你将创建一个新的 Nuxt 应用程序，并使用 Cloudflare Pages 进行部署。

## 使用 `create-cloudflare` CLI(C3)创建新项目

`create-cloudflare` CLI (C3)](/pages/get-started/c3/) 将为 Cloudflare 页面配置你的 Nuxt 站点。在终端中运行以下命令创建新的 Nuxt 网站：

```sh
$ npm create cloudflare@latest my-nuxt-app -- --framework=nuxt
```

C3 会询问一系列设置问题，并使用 [`nuxi`(官方 Nuxt CLI)](https://github.com/nuxt/cli) 创建一个新项目。C3 还将安装必要的适配器和 [Wrangler CLI](/workers/wrangler/install-and-update/#check-your-wrangler-version)。

创建项目后，C3 将使用默认 Nuxt 模板生成一个新的 `my-nuxt-app` 目录，该模板已更新为与 Cloudflare Pages 完全兼容。

创建新项目时，C3 会让你选择通过 [Direct Upload](/pages/how-to/use-direct-upload-with-continuous-integration/)部署应用程序的初始版本。你可以在项目目录下运行以下命令，随时重新部署应用程序：

```sh
$ npm run deploy
```

{{<Aside type="note" header="Git integration">}}

通过 C3 创建的初始部署称为 [Direct Upload](/pages/get-started/direct-upload/)。要通过 Pages Git 集成设置部署，请参阅下面的 [Git 集成](#git-integration) 部分。

{{</Aside>}}

## 配置和部署不带 C3 的项目

要在没有 C3 的情况下部署 Nuxt 项目，请遵循 [Nuxt 入门指南](https://nuxt.com/docs/getting-started/installation)。设置好 Nuxt 项目后，选择 [Git 整合指南](/pages/get-started/git-integration/) 或 [Direct Upload 指南](/pages/get-started/direct-upload/) 在 Cloudflare Pages 上部署 Nuxt 项目。

{{<render file="/_framework-guides/_git-integration.md">}}

### 创建 GitHub 仓库

{{<render file="/_framework-guides/_create-gh-repo.md">}}

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

3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

{{<pages-build-preset framework="nuxt-js">}}

你可以选择自定义**项目名称**字段。它默认为 GitHub 仓库的名称，但不必与之匹配。项目名称**值将被指定为`*.pages.dev`子域。

4. 完成配置后，选择**保存并部署**。

查看正在进行的第一个部署管道。Pages 按照指定安装所有依赖项并构建项目。Cloudflare Pages 会自动重建项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将更改部署到生产环境之前，用一个真实的 URL 来预览对项目所做的更改。

## 在 Nuxt 应用程序中使用绑定

{{<render file="/_framework-guides/_bindings_definition.md">}}

### 为本地开发设置绑定

通过 C3 创建的项目附带 `nitro-cloudflare-dev`，这是一个 `nitro `模块，可简化开发过程中的绑定工作：

```typescript
---
filename: nuxt.config.ts
highlights: [2]
---
export default defineNuxtConfig({
  modules: ["nitro-cloudflare-dev"],
});
```

该模块由 [`getPlatformProxy`辅助函数](/workers/wrangler/api#getplatformproxy) 提供支持。`getPlatformProxy `会自动检测项目的 `wrangler.toml `文件中定义的任何绑定，并在本地开发中模拟这些绑定。查看[Wrangler 关于绑定的配置信息](/workers/wrangler/configuration/#bindings)，了解如何在`wrangler.toml`中配置绑定的更多信息。

{{<Aside type="note">}}

`wrangler.toml `目前**仅**用于本地开发。其中指定的绑定无法远程使用。

{{</Aside>}}

### 为已部署的应用程序设置绑定

要访问已部署应用程序中的绑定，你需要在 Cloudflare 控制面板中[配置你的绑定](/pages/functions/bindings/)。

### 为 TypeScript 项目添加绑定

要获得适当的类型支持，你需要在项目根目录下创建一个新的 `env.d.ts` 文件，并声明一个 [binding](/pages/functions/bindings/)。

下面是添加 `KVNamespace` 绑定的示例：

```ts
---
filename: env.d.ts
highlight: [9]
---
import { CfProperties, Request, ExecutionContext, KVNamespace } from '@cloudflare/workers-types';

declare module 'h3' {
    interface H3EventContext {
        cf: CfProperties,
        cloudflare: {
          request: Request,
          env: {
            MY_KV: KVNamespace,
          }
          context: ExecutionContext,
        };
    }
}
```

### 在 Nuxt 应用程序中访问绑定

在 Nuxt 中，通过 [Server Routes and Middleware](https://nuxt.com/docs/guide/directory-structure/server#server-directory) 添加服务器端代码。`defineEventHandler() `方法用于定义你的 API 端点，在这些端点中，你可以通过提供的 `context `字段访问 Cloudflare 的上下文。通过 `context` 字段，你可以访问为你的应用程序设置的任何绑定。

下面的代码块展示了在 Nuxt 中访问 KV 命名空间的示例。


{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}

```javascript
---
filename: server/api/hello.js
highlight: [2]
---
export default defineEventHandler(({ context }) => {
  const MY_KV = context.cloudflare.env.MY_KV;

  return {
    // ...
  };
});
```

{{</tab>}}
{{<tab label="ts">}}

```typescript
---
filename: server/api/hello.ts
highlight: [2]
---
export default defineEventHandler(({ context }) => {
  const MY_KV = context.cloudflare.env.MY_KV;

  return {
    // ...
  };
});
```

{{</tab>}}
{{</tabs>}}

{{<render file="/_framework-guides/_learn-more.md" withParameters="Nuxt">}}