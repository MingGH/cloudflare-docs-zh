---
pcx_content_type: how-to
title: Analog
meta:
  description: The fullstack Angular meta-framework
---

# Analog

[Analog](https://analogjs.org/)是Angular的全栈元框架，由[Vite](https://vitejs.dev/) 和[Nitro](https://nitro.unjs.io/)提供支持。

在本指南中，你将创建一个新的模拟应用程序，并使用 Cloudflare Pages 进行部署。

## 使用 `create-cloudflare` 创建一个新项目

创建新的模拟项目并部署到 Cloudflare 页面的最简单方法是使用 [`create-cloudflare`](https://www.npmjs.com/package/create-cloudflare) CLI(也称为 C3)。要开始使用，请打开终端并运行

{{<tabs labels="npm | pnpm | bun">}}
{{<tab label="npm" default="true">}}

```sh
$ npm create cloudflare@latest my-analog-app -- --framework=analog
```

{{</tab>}}
{{<tab label="pnpm">}}

```sh
$ pnpm create cloudflare@latest my-analog-app --framework=analog
```

{{</tab>}}
{{<tab label="bun">}}

```sh
$ bun create cloudflare@latest my-analog-app --framework=analog
```

{{</tab>}}
{{</tabs>}}

C3 将引导你完成设置过程，并使用官方模拟创建工具 `create-analog `创建一个新项目。它还会安装必要的适配器和 [Wrangler CLI](/workers/wrangler/install-and-update/#check-your-wrangler-version)。

{{<Aside type="note" header="Deployment">}}

C3 工作流程的最后一步是将应用程序部署到 Cloudflare。有关部署选项的更多信息，请参阅下面的 [部署](#deployment) 部分。

{{</Aside>}}

## 绑定

{{<render file="/_framework-guides/_bindings_definition.md">}}

在 Analog 中，服务器端代码可通过 [API Routes](https://analogjs.org/docs/features/api/overview) 添加。`defineEventHandler() `方法用于定义你的 API 端点，你可在其中通过提供的 `context `字段访问 Cloudflare 的上下文。通过 `context` 字段，你可以访问为你的应用程序设置的任何绑定。

以下代码块显示了在 Analog 中访问 KV 命名空间的示例。

```typescript
---
filename: src/server/routes/v1/hello.ts
highlight: [2]
---
export default defineEventHandler(async ({ context }) => {
  const { MY_KV } = context.cloudflare.env;
  const greeting = (await MY_KV.get("greeting")) ?? "hello";

  return {
    greeting,
  };
});
```

### 在开发中设置绑定

通过 C3 创建的项目会安装一个 Nitro 模块，该模块可简化开发过程中的绑定工作：

```typescript
---
filename: vite.config.ts
---
const devBindingsModule = async (nitro: Nitro) => {
  if (nitro.options.dev) {
    nitro.options.plugins.push('./src/dev-bindings.ts');
  }
};

export default defineConfig({
  ...
  plugins: [analog({
    nitro: {
      preset: "cloudflare-pages",
      modules: [devBindingsModule]
    }
  })],
  ...
});
```

该模块反过来会加载一个插件，在 dev 中为事件上下文添加绑定：

```typescript
---
filename: src/dev-bindings.ts
---
import { NitroApp } from 'nitropack';
import { defineNitroPlugin } from 'nitropack/dist/runtime/plugin';

export default defineNitroPlugin((nitroApp: NitroApp) => {
  nitroApp.hooks.hook('request', async (event) => {
    const _pkg = 'wrangler'; // Bypass bundling!
    const { getPlatformProxy } = (await import(
      _pkg
    )) as typeof import('wrangler');
    const platform = await getPlatformProxy();

    event.context.cf = platform['cf'];
    event.context.cloudflare = {
      env: platform['env'] as unknown as Env,
      context: platform['ctx'],
    };
  });
});
```

在上述代码中，`getPlatformProxy `辅助函数将自动检测项目的 `wrangler.toml `文件中定义的任何绑定，并在本地开发中模拟这些绑定。请查看[Wrangler 关于绑定的配置信息](/workers/wrangler/configuration/#bindings)，了解如何在`wrangler.toml`中配置绑定的更多信息。

可使用以下命令从 `wrangler.toml` 生成 `Env` 类型(由 `context.cloudflare.env` 使用)的新类型定义：

```sh
$ npm run cf-typegen
```

每次向 `wrangler.toml` 添加新绑定时，都应执行此操作。

### 在部署的应用程序中设置绑定

要访问已部署应用程序中的绑定，你需要在 Cloudflare 控制面板中[配置你的绑定](/pages/functions/bindings/)。

## 部署

创建新项目时，C3 会让你选择通过 [Direct Upload](/pages/how-to/use-direct-upload-with-continuous-integration/)部署应用程序的初始版本。你可以在项目目录下运行以下命令，随时重新部署应用程序：

```sh
$ npm run deploy
```

{{<render file="/_framework-guides/_git-integration.md">}}

### 创建 GitHub 仓库

{{<render file="/_framework-guides/_create-gh-repo.md">}}

```sh
# Skip the following three commands if you have built your application
# using C3 or already committed your changes
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

{{<pages-build-preset framework="analog">}}

你可以选择自定义**项目名称**字段。它默认为 GitHub 仓库的名称，但不必与之匹配。项目名称**值将被指定为`*.pages.dev`子域。

4. 完成配置后，选择**保存并部署**。

查看正在进行的第一个部署管道。Pages 按照指定安装所有依赖项并构建项目。Cloudflare Pages 会自动重建项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将更改部署到生产环境之前，用一个真实的 URL 来预览对项目所做的更改。