---
pcx_content_type: how-to
title: Advanced mode
weight: 9
---

# 高级模式

高级模式允许你使用 `_worker.js` 文件而不是 `/functions` 目录开发页面函数。

在某些情况下，Pages Functions 内置的基于文件路径的路由和中间件系统并不适合现有的应用程序。你的 Worker 可能比较复杂，难以拼接到 Pages 基于文件的路由系统中。在这种情况下，Pages 提供了在 Pages 项目的输出目录中定义 `_worker.js` 文件的功能。

使用 `_worker.js` 文件时，整个 `/functions` 目录将被忽略，包括其路由和中间件特性。相反，`_worker.js` 文件将被部署，并且必须使用 [Module Worker 语法](/workers/runtime-apis/handlers/fetch/)编写。如果从未使用过模块语法，请参阅[JavaScript 模块博文](https://blog.cloudflare.com/workers-javascript-modules/) 了解更多信息。使用模块语法，JavaScript 框架可以生成 Worker，作为页面输出目录内容的一部分。

## 设置Function

在高级模式下，你的 Function 将完全控制所有传入你域的 HTTP 请求。你的 Function 必须向项目的静态资产发出或转发请求。否则将导致错误或不需要的行为。你的 Function 必须使用模块语法编写。

在输出目录中创建 `_worker.js` 文件后，添加以下代码片段：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
---
filename: _worker.js
---
export default {
  async fetch(request, env) {
    const url = new URL(request.url);
    if (url.pathname.startsWith('/api/')) {
      // TODO: Add your custom /api/* logic here.
      return new Response('Ok');
    }
    // Otherwise, serve the static assets.
    // Without this, the Worker will error and no assets will be served.
    return env.ASSETS.fetch(request);
  },
}
```

{{</tab>}}
{{<tab label="ts">}}
```ts
// Note: You would need to compile your TS into JS and output it as a `_worker.js` file. We do not read `_worker.ts`

interface Env {
  ASSETS: Fetcher;
}

export default {
  async fetch(request, env): Promise<Response> {
    const url = new URL(request.url);
    if (url.pathname.startsWith('/api/')) {
      // TODO: Add your custom /api/* logic here.
      return new Response('Ok');
    }
    // Otherwise, serve the static assets.
    // Without this, the Worker will error and no assets will be served.
    return env.ASSETS.fetch(request);
  },
} satisfies ExportedHandler<Env>;
```

{{</tab>}}
{{</tabs>}}

在上述代码中，你已将 Function 配置为在所有以 `/api/` 为标题的请求下返回响应。否则，你的 Function 将返回静态资产。

* 使用`env.ASSETS.fetch()`函数，可以根据给定请求返回资产。
* `env` 是包含环境变量和绑定的对象。
* `ASSETS` 是一个默认的函数绑定，允许你的函数与 Pages 的资产服务资源进行通信。
* `fetch()`调用页面的资产服务资源，并提供所请求的资产。

## 从Works迁移

要将现有 Worker 移植到 Pages 项目，请复制 Worker 代码并粘贴到新的 `_worker.js` 文件中。然后在 `_worker.js` 中添加以下代码段，以处理静态资产：

```ts
---
filename: _worker.js
---
return env.ASSETS.fetch(request);
```

## 部署你的Function

在设置了新Function或将 Worker 迁移到 `_worker.js`后，请确保将 `_worker.js`文件放在 Pages 的项目输出目录中。通过 Git 集成部署项目，使高级模式生效。