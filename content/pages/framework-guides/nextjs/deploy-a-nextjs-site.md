---
pcx_content_type: how-to
title: Full-stack deployment
meta:
  description: Deploy a full-stack Next.js site (recommended).
---

# Next.js

[Next.js](https://nextjs.org)是一个用于创建网站和应用程序的开源 React 框架。在本指南中，你将创建一个新的 Next.js 应用程序，并使用 Cloudflare Pages 进行部署。

本指南将指导你如何通过 [`next-on-pages`](https://github.com/cloudflare/next-on-pages/tree/main/packages/next-on-pages/docs) 适配器部署使用 [Edge Runtime](https://nextjs.org/docs/app/api-reference/edge) 的全栈 Next.js 项目。

## 使用 `create-cloudflare` CLI(C3)创建新项目

`create-cloudflare` CLI (C3)](/pages/get-started/c3/) 将为 Cloudflare 页面配置 Next.js 网站。在终端中运行以下命令创建新的 Next.js 网站：

```sh
$ npm create cloudflare@latest my-next-app -- --framework=next
```

C3 会询问一系列设置问题。C3 还将安装必要的依赖项，包括 [Wrangler](/workers/wrangler/install-and-update/#check-your-wrangler-version) CLI 和 `@cloudflare/next-on-pages` 适配器。

创建项目后，C3 将使用默认 Next.js 模板生成一个新的 `my-next-app` 目录，该模板已更新为与 Cloudflare Pages 完全兼容。

创建新项目时，C3 会让你选择通过 [Direct Upload](/pages/how-to/use-direct-upload-with-continuous-integration/)部署应用程序的初始版本。你可以在项目目录下运行以下命令，随时重新部署应用程序：

```sh
$ npm run deploy
```

{{<Aside type="note" header="Git integration">}}

通过 C3 创建的初始部署称为 [Direct Upload](/pages/get-started/direct-upload/)。要通过 Pages Git 集成设置部署，请参阅下面的 [Git 集成](#git-integration) 部分。

{{</Aside>}}

## 配置和部署不带 C3 的项目

如果你已经有一个 Next.js 项目，或希望不使用 C3 而手动创建和部署一个项目，Cloudflare 建议你使用 `@cloudflare/next-on-pages`，并参考其 [README](https://github.com/cloudflare/next-on-pages/tree/main/packages/next-on-pages)，以获取帮助你开发和部署项目的说明和其他信息。

{{<render file="/_framework-guides/_git-integration.md">}}

### 创建一个新的 GitHub 仓库

{{<render file="/_framework-guides/_create-gh-repo.md">}}

```sh
# Skip the following three commands if you have built your application
# using C3 or already committed your changes
$ git init
$ git add .
$ git commit -m "Initial commit"

$ git branch -M main
$ git remote add origin https://github.com/<your-gh-username>/<repository-name>
$ git push -u origin main
```
### 通过 Cloudflare 面板将你的应用程序连接到 GitHub 仓库

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。

如果你尚未授权访问你的 GitHub 帐户，系统将要求你进行授权。Cloudflare 需要这样才能从源代码监控和部署你的项目。如果你愿意，可以缩小对特定存储库的访问范围。不过，当你想在 Cloudflare 页面添加更多版本库时，你必须手动更新此列表[在你的 GitHub 设置中](https://github.com/settings/installations)。

3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

{{<pages-build-preset framework="next-js">}}

你可以选择自定义**项目名称**字段。它默认为 GitHub 仓库的名称，但不必与之匹配。项目名称**值将被指定为`*.pages.dev`子域。

4. 完成配置后，选择**保存并部署**。

你可以查看正在进行的首次部署管道。Pages 会按照指定安装所有依赖项并构建项目。Cloudflare Pages 会自动重建项目，并在每次推送新提交时进行部署。

此外，你还可以访问 [预览部署](/pages/configuration/preview-deployments/)，它可以重复拉取请求的构建和部署过程。有了它们，你就可以在将更改部署到生产环境之前，用一个真实的 URL 来预览对项目所做的更改。

## 在 Next.js 应用程序中使用绑定

{{<render file="/_framework-guides/_bindings_definition.md">}}

### 为本地开发设置绑定

{{<Aside type="note">}}

使用 C3 创建的项目默认设置了本地开发绑定。

{{</Aside>}}

要设置用于本地开发的绑定，你需要使用 [`@cloudflare/next-on-pages/next-dev`](https://github.com/cloudflare/next-on-pages/tree/main/internal-packages/next-dev) 提供的 `setupDevPlatform` 函数。`setupDevPlatform `根据项目的 [`wrangler.toml`](/workers/wrangler/configuration/)文件设置平台模拟，以便 Next.js 应用程序在本地使用。

例如，要在本地使用 KV 绑定，请打开 Next.js 配置文件并添加

{{<tabs labels="next.config.mjs | next.config.(js|cjs)">}}
{{<tab label="next.config.mjs">}}

```js
---
filename: next.config.mjs
highlight: [1-7]
---
import { setupDevPlatform } from '@cloudflare/next-on-pages/next-dev'

// note: the if statement is present because you
//       only need to use the function during development
if (process.env.NODE_ENV === 'development') {
  await setupDevPlatform()
}

/** @type {import('next').NextConfig} */
const nextConfig = {}

export default nextConfig
```

{{</tab>}}
{{<tab label="next.config.(js|cjs)">}}

```js
---
filename: next.config.js / next.config.cjs
highlight: [1-6]
---
// note: the if statement is present because you
//       only need to use the function during development
if (process.env.NODE_ENV === "development") {
  const { setupDevPlatform } = require("@cloudflare/next-on-pages/next-dev")
  setupDevPlatform()
}

/** @type {import('next').NextConfig} */
const nextConfig = {}

module.exports = nextConfig
```

{{</tab>}}
{{</tabs>}}

确保在项目根目录下有一个 `wrangler.toml` 文件，其中包含名为 `MY_KV` 的 KV 绑定声明：

```toml
---
filename: wrangler.toml
highlight: [5-7]
---
name = "my-next-app"

compatibility_flags = ["nodejs_compat"]

[[kv_namespaces]]
binding = "MY_KV"
id = "<YOUR_KV_NAMESPACE_ID>"
```

### 为已部署的应用程序设置绑定

要访问已部署应用程序中的绑定，你需要 [配置](/pages/functions/bindings/) 任何必要的绑定，并通过 Cloudflare 面板中的项目设置页面将它们连接到你的项目。

### 为 Typescript 项目添加绑定

如果你的项目使用的是 TypeScript，你需要设置适当的类型支持，以便以类型安全和方便的方式访问绑定。

要获得适当的类型支持，你需要在项目中创建一个新的 `env.d.ts` 文件，并使用你的 [bindings](/pages/functions/bindings/) 扩展 `CloudflareEnv`(由 `getRequestContext`使用)接口。

{{<Aside type="note">}}

使用 C3 创建的项目有一个默认的 `env.d.ts` 文件。

{{</Aside>}}

下面是一个如何添加 `KVNamespace` 绑定的示例：

```ts
---
filename: env.d.ts
highlight: [7]
---
interface CloudflareEnv {
  // The KV Namespace binding type used here comes
  // from `@cloudflare/workers-types`. To use it in such
  // a way make sure that you have installed the package
  // as a dev dependency and you have added it to your
  //`tsconfig.json` file under `compilerOptions.types`.
  MY_KV: KVNamespace
}
```

### 应用程序中的访问绑定

可使用 `@cloudflare/next-on-pages` 公开的 [`getRequestContext`]函数(https://github.com/cloudflare/next-on-pages/blob/3846730c4a0d12/packages/next-on-pages/README.md#cloudflare-platform-integration)访问本地和远程绑定。下面的代码示例展示了如何在 App Router 应用程序的 `hello` API 路由中访问它们。

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}

```js
---
filename: app/api/hello/route.js
highlight: [1, 9]
---
import { getRequestContext } from '@cloudflare/next-on-pages'

export const runtime = 'edge'

// ...

export async function GET(request) {
  // this is the KV binding you defined in the wrangler.toml file
  const myKv = getRequestContext().env.MY_KV

  // get a value from the namespace
  const kvValue = await myKv.get(`kvTest`) || false

  return new Response(`The value of kvTest in MY_KV is: ${kvValue}`)
}
```

{{</tab>}}
{{<tab label="ts">}}

```ts
---
filename: app/api/hello/route.ts
highlight: [2, 10]
---
import type { NextRequest } from 'next/server'
import { getRequestContext } from '@cloudflare/next-on-pages'

export const runtime = 'edge'

// ...

export async function GET(request: NextRequest) {
  // this is the KV binding you defined in the wrangler.toml file
  const myKv = getRequestContext().env.MY_KV

  // get a value from the namespace
  const kvValue = await myKv.get(`kvTest`) || false

  return new Response(`The value of kvTest in MY_KV is: ${kvValue}`)
}
```

{{</tab>}}
{{</tabs>}}

## `Image`组件

Cloudflare 网络不提供与 Vercel 网络相同的图像优化支持。因此，Next.js 的 `<Image />` 组件与在 Vercel 网络中的行为不同。

- 如果将应用程序作为静态网站构建，`<Image />` 组件将不提供任何图像。

- 如果使用`@cloudflare/next-on-pages`构建应用程序，该组件将正常工作，但不会执行任何图像优化(无论你向其传递的[props](https://react.dev/learn/passing-props-to-a-component)是什么)。

这两种情况都可以通过为`<image />`组件设置适当的[loaders](https://nextjs.org/docs/pages/api-reference/components/image#loader)来改善，你可以使用任何你想要的图像优化服务。要使用 [Cloudflare Images](/images/)，请参阅 [resize with Cloudflare Workers](/images/transform-images/transform-via-workers/)。

## 建议的开发工作流程

在开发 `next-on-pages` 应用程序时，这是 Cloudflare 推荐的开发工作流程：

### 使用标准 Next.js 开发服务器进行开发

Next.js提供的[标准开发服务器](https://nextjs.org/docs/getting-started/installation#run-the-development-server)是获得快速、完美开发体验的最佳选择。`next-dev `子模块(如[本地绑定](#set-up-bindings-for-local-development)部分所述)使你可以使用 Next.js 的标准开发服务器，同时仍可访问你的 Cloudflare 绑定。

### 在本地构建并预览应用程序

为确保你的应用程序以完全兼容 Cloudflare Pages 的方式构建，在部署之前，或在开发过程中检查应用程序的正确性时，你需要使用 Cloudflare 的 `workerd` JavaScript 运行时在本地构建和预览应用程序。

如果你使用 C3 创建了项目，请运行

```sh
$ npm run preview
```

如果创建的项目没有 C3，请运行：

```sh
$ npx @cloudflare/next-on-pages@1
```

然后运行

```sh
$ npx wrangler pages dev .vercel/output/static
```

{{<Aside type="note">}}

[`wrangler pages dev`](/workers/wrangler/commands/#dev-1)命令需要使用[`nodejs_compat`](/workers/configuration/compatibility-dates/#nodejs-compatibility-flag)兼容性标志运行应用程序。`nodejs_compat `标志可在项目的 `wrangler.toml `文件中指定，或作为内联参数提供给命令：`--compatibility-flag=nodejs_compat`。

{{</Aside>}}


### 部署应用程序并迭代

在本地预览应用程序后，你可以将其部署到 Cloudflare 页面(可通过 [Direct Uploads](/pages/get-started/direct-upload/) 或 [Git integration](/pages/configuration/git-integration/) 两种方式)，并在此过程中反复进行新的更改。

## 疑难解答
回顾使用 `next-on-pages `开发 Next.js 应用程序时可能遇到的常见错误和问题。

{{<Aside type="note" header="Tip">}}

使用官方[next-on-pages ESLint 插件](https://www.npmjs.com/package/eslint-plugin-next-on-pages)(尤其是 `recommended `规则)
可以帮助你避免类似本节所述的问题，直接在你的集成开发环境中提供有用和可操作的反馈。

{{</Aside>}}


### 边缘计算时间

在 Cloudflare 页面上运行时，Next.js 项目中的所有服务器端路由必须配置为 Edge 运行时路由。你必须在每个服务器端路由中添加 `export const runtime = 'edge'`。


{{<Aside type="note">}}

如果你使用的是页面路由器，对于页面路由，你需要使用`'experimental-edge'`而不是`'edge'`。

{{</Aside>}}

### App 路由器

#### Not found

在构建过程中，Next.js 会为你的应用程序生成一个 `not-found`路由。在某些情况下，Next.js 会检测到路由需要服务器端逻辑(尤其是在根布局组件中执行计算时)，Next.js 可能会创建一个 Node.js 无服务器函数(因此与 `@cloudflare/next-on-pages`不兼容)。

为防止出现这种不兼容性，Cloudflare 建议始终提供一个自定义的 `not-found`路由，明确选择边缘运行时：

```ts
---
filename: (src/)app/not-found.(jsx|tsx)
---

export const runtime = 'edge'

export default async function NotFound() {
    // ...
    return (
        // ...
    )
}
```

{{<Aside type="note">}}

使用 C3 创建的项目已经创建了默认的自定义 `not-found`页面。

{{</Aside>}}

#### `generateStaticParams` `生成静态参数

在 [`/app`目录](https://nextjs.org/docs/getting-started/project-structure) 中进行静态网站生成 (SSG) 并使用 [`generateStaticParams`](https://nextjs.org/docs/app/api-reference/functions/generate-static-params)函数时，Next.js 默认会尝试通过 Node.js 无服务器函数按需处理非静态生成路由的请求，因此这些函数与 `@cloudflare/next-on-pages` 不兼容。

为避免此类问题，请确保按照 [上文](#edge-runtime)所述，在边缘运行时选择路由(但这会使 SSG 失效，因为目前 Next.js 在边缘运行时不支持 SSG)。

或者，也可以通过指定 `false` [`dynamicParams`](https://nextjs.org/docs/app/api-reference/file-conventions/route-segment-config#dynamicparams) 来退出动态路由处理：

```diff
---
filename: app/my-example-page/[slug]/page.jsx
---
+ export const dynamicParams = false

// ...
```

#### 顶级`getRequestContext`(获取请求上下文

不能在路由文件的顶层调用 `getRequestContext` 函数，也不能在文件初始化过程中以任何方式触发函数调用。

`getRequestContext` 必须在请求处理流程逻辑中调用，而不是以全局/无条件的方式在导入文件后立即触发。

例如，下面是 `getRequestContext` 的错误用法：

```js
---
filename: app/api/myvar/route.js
highlight: [5]
---
import { getRequestContext } from '@cloudflare/next-on-pages'

export const runtime = 'edge'

const myVariable = getRequestContext().env.MY_VARIABLE

export async function GET(request) {
  return new Response(myVariable)
}
```

上面的例子可以用下面的方法解决：

```js
---
filename: app/api/myvar/route.js
highlight: [6]
---
import { getRequestContext } from '@cloudflare/next-on-pages'

export const runtime = 'edge'

export async function GET(request) {
  const myVariable = getRequestContext().env.MY_VARIABLE
  return new Response(myVariable)
}
```

### Pages 路由

#### `getStaticPaths`

在 [`/pages`目录](https://nextjs.org/docs/getting-started/project-structure) 中进行静态网站生成 (SSG) 并使用 [`getStaticPaths`](https://nextjs.org/docs/pages/api-reference/functions/get-static-paths)函数时，Next.js 默认会尝试通过 Node.js 无服务器函数按需处理对非静态生成路由的请求，因此这些函数与 `@cloudflare/next-on-pages` 不兼容。

为避免此类问题，请确保按照 [上文](#edge-runtime)所述，在边缘运行时选择路由(但这会使 SSG 失效，因为目前 Next.js 在边缘运行时不支持 SSG)。

或者，也可以通过指定 [false `fallback`](https://nextjs.org/docs/pages/api-reference/functions/get-static-paths#fallback-false) 来退出动态路由处理：


```diff
---
filename: pages/my-example-page/[slug].jsx
---
// ...

export async function getStaticPaths() {
    // ...

    return {
        paths,
+       fallback: false,
	}
}
```

{{<Aside type="warning">}}

如果选择后者，请注意 `paths `数组不能为空，因为空的 `paths `数组会导致 Next.js 忽略所提供的 `fallback `值。在构建时，请确保数组中至少有一个条目。

{{</Aside>}}

{{<render file="/_framework-guides/_learn-more.md" withParameters="Next.js">}}