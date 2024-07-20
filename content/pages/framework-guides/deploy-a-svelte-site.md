---
pcx_content_type: how-to
title: Svelte
---

# Svelte

[Svelte](https://svelte.dev)是一个日益流行的开源框架，用于构建用户界面和网络应用程序。与大多数框架不同，Svelte 主要是一个编译器，可将组件代码转换为高效的 JavaScript，并在应用程序状态发生变化时，以外科手术的方式更新 DOM。

在本指南中，你将创建一个新的 Svelte 应用程序，并使用 Cloudflare Pages 进行部署。
你将使用 [`SvelteKit`](https://kit.svelte.dev/)，这是用于构建各种规模网络应用程序的 Svelte 官方框架。

## 设置新项目

使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI (C3) 建立新项目。C3 将创建一个新的项目目录，启动 Svelte 的官方设置工具，并提供即时部署选项。


要使用 `create-cloudflare` 创建新的 Svelte 项目，请运行以下命令：

```sh
$ npm create cloudflare@latest my-svelte-app -- --framework=svelte
```

SvelteKit 将提示你进行自定义选择。对于模板选项，请选择应用程序/项目选项之一。其余答案不会影响本指南的其余部分。选择适合你项目的选项。

然后，`create-cloudflare`会安装依赖项，包括[Wrangler](/workers/wrangler/install-and-update/#check-your-wrangler-version) CLI和`@sveltejs/adapter-cloudflare`适配器，并向你提出设置问题。

安装完项目依赖项后，启动应用程序：

```sh
$ npm run dev
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## SvelteKit Cloudflare 配置

要在 Cloudflare 页面中使用 SvelteKit，你需要在应用程序中添加 [Cloudflare 适配器](https://kit.svelte.dev/docs/adapter-cloudflare)。

{{<render file="_c3-adapter.md">}}

1. 在终端运行 `npm i --save-dev @sveltejs/adapter-cloudflare` 安装 Cloudflare 适配器。
2. 在 `svelte.config.js` 中加入适配器：

```diff
---
filename: svelte.config.js
---
- import adapter from '@sveltejs/adapter-auto';
+ import adapter from '@sveltejs/adapter-cloudflare';

/** @type {import('@sveltejs/kit').Config} */
const config = {
  kit: {
    adapter: adapter(),
    // ... truncated ...
  }
};

export default config;
```

3. (使用 TypeScript 时需要)包含对环境变量的支持。包含 KV 命名空间和其他存储对象的 `env` 对象与上下文和缓存一起通过平台属性传递给 SvelteKit，这意味着你可以在钩子和端点中访问它。例如

```diff
---
filename:  src/app.d.ts
---

declare namespace App {
    interface Locals {}

+   interface Platform {
+       env: {
+           COUNTER: DurableObjectNamespace;
+       };
+       context: {
+           waitUntil(promise: Promise<any>): void;
+       };
+       caches: CacheStorage & { default: Cache }
+   }

    interface Session {}

    interface Stuff {}
}

```

4. 使用 `env` 访问端点中添加的 KV 或 Durable 对象(或通常任何 [绑定](/pages/functions/bindings/))：

```js
export async function post(context) {
  const counter = context.platform.env.COUNTER.idFromName("A");
}
```

{{<Aside type="note">}}

除 Cloudflare 适配器外，请查看你可以在项目中使用的其他适配器：

- [`@sveltejs/adapter-auto`](https://www.npmjs.com/package/@sveltejs/adapter-auto)

  SvelteKit 的默认适配器会自动选择适合当前环境的适配器。如果使用该适配器，则[无需配置](https://kit.svelte.dev/docs/adapter-auto)。不过，默认适配器会给本地开发带来一些不利因素，因为它无法知道应用程序将部署到哪个平台。

要解决这个问题，请向 SvelteKit 提供一个 `CF_PAGES` 变量，以便适配器可以检测页面平台。例如，在本地构建应用程序时：`CF_PAGES=1 vite build`。

- [`@sveltejs/adapter-static`](https://www.npmjs.com/package/@sveltejs/adapter-static)
  只生成客户端静态资产(无服务器端渲染)，与 Cloudflare Pages 兼容。
  请查阅 [SvelteKit 官方文档](https://kit.svelte.dev/docs/adapter-static) 了解如何设置适配器。请注意，如果你决定使用此适配器，则构建目录将从 `.svelte-kit/cloudflare`变为 `build`。你还必须相应配置 Cloudflare Pages 应用程序的构建目录。

{{</Aside>}}

{{<Aside type="warning">}}

如果你使用的适配器与默认的 SvelteKit 适配器不同，请记得在尝试部署前将适配器设置更改提交并推送到 GitHub 仓库。

{{</Aside>}}

## 使用 Cloudflare 页面部署

{{<render file="_deploy-via-c3.md" withParameters="Svelte">}}

### 通过 Cloudflare 控制面板部署

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。

如果你尚未授权访问你的 GitHub 账户，系统将要求你进行授权。Cloudflare 需要此授权才能从你的 GitHub 账户部署项目。你可以缩小 Cloudflare 对特定存储库的访问范围。但是，当你想在 Cloudflare 页面添加更多的软件源时，你必须手动更新此列表[在你的 GitHub 设置中](https://github.com/settings/installations)。

选择你创建的新 GitHub 仓库，并在**设置构建和部署**中提供以下信息：

<div>

{{<pages-build-preset framework="sveltekit">}}

</div>

你可以选择自定义**项目名称**字段。它默认为 GitHub 仓库的名称，但不必与之匹配。 **Project name** 值将被指定为`*.pages.dev`子域。

完成配置后，单击**保存和部署**按钮。

你将看到第一个部署管道正在进行中。页面会安装所有依赖项，并按指定方式构建项目。

Cloudflare Pages 会自动重建你的项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将项目中的更改部署到生产环境之前，用一个真实的 URL 进行预览。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

## Functions setup

在 SvelteKit 中，函数被写成端点。项目根目录 `/functions`中包含的函数将不会包含在部署中，部署会编译成一个 `_worker.js`文件。

要获得与 Pages Functions [`onRequests`](/pages/functions/api-reference/#onrequests) 相同的功能，你需要在 SvelteKit 中编写标准请求处理程序。例如，以下 TypeScript 文件的行为类似于 `onRequestGet`：

```ts
---
filename: src/routes/random/+server.ts
---
import type { RequestHandler } from './$types';

export const GET = (({ url }) => {
  return new Response(String(Math.random()));
}) satisfies RequestHandler;
```

{{<Aside type= "note" header="SvelteKit API Routes">}}
有关 SvelteKit API 路由的更多信息，请参阅 [SvelteKit 文档](https://kit.svelte.dev/docs/routing#server)。
{{</Aside>}}

{{<render file="/_framework-guides/_learn-more.md" withParameters="Svelte">}}