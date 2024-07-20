---
pcx_content_type: how-to
title: 调试和日志记录
weight: 12
---

# 调试和日志记录

使用 Cloudflare 仪表板或 [Wrangler CLI](/workers/wrangler/commands/#deployment-tail)访问功能日志。

日志是一种功能强大的调试工具，可帮助你在部署 Pages 函数后对其行为进行测试和监控。Pages 项目的每次部署都有日志。

日志提供有关事件的详细信息，并能让人深入了解：

* 你的功能请求成功或失败。
* 函数抛出的未捕获异常。
* 在函数中声明自定义 `console.log`s 。
* 不容易复制的生产问题。
* 实时查看传入应用程序的请求。

启动记录会话有两种方法：

1. [在终端](/pages/functions/debugging-and-logging/#view-logs-with-wrangler)运行 `wrangler pages deployment tail`。
2. 使用 [Cloudflare dashboard](/pages/functions/debugging-and-logging/#view-logs-in-the-cloudflare-dashboard)。

## 添加自定义日志

自定义日志是你自己添加到函数中的 `console.log()` 语句。在为包含这些 Functions 的部署生成流式日志时，这些语句将同时出现在 `wrangler pages deployment tail` 和仪表板输出中。

下面是在页面函数中自定义 `console.log` 语句的示例：

```js
---
filename:
---
export async function onRequest(context) {
  console.log(`[LOGGING FROM /hello]: Request came from ${context.request.url}`);

  return new Response("Hello, world!");
}
```

部署完上述代码后，在终端运行 `wrangler pages deployment tail`。然后访问 Function 所在的路由。终端将显示

![运行 `wrangler 页面部署 tail`](/images/pages/platform/functions/wrangler-custom-logs.png)

仪表板将显示

![按照上述步骤访问仪表板中的自定义日志](/images/pages/platform/functions/dash-custom-logs.png)

## 使用 Wrangler 查看日志

`wrangler pages deployment tail` 使开发人员能够对特定项目和部署的日志进行直播。

要开始使用，请在 Pages 项目目录下运行 `wrangler pages deployment tail`。这将在本地终端中记录任何传入应用程序的请求。

每个 `wrangler 页面部署 tail` 日志的输出都是一个结构化的 JSON 对象：

```js
{
  "outcome": "ok",
  "scriptName": null,
  "exceptions": [
    {
      "stack": "    at src/routes/index.tsx17:4\n    at new Promise (<anonymous>)\n",
      "name": "Error",
      "message": "An error has occurred",
      "timestamp": 1668542036110
    }
  ],
  "logs": [],
  "eventTimestamp": 1668542036104,
  "event": {
    "request": {
      "url": "https://pages-fns.pages.dev",
      "method": "GET",
      "headers": {},
      "cf": {}
    },
    "response": {
      "status": 200
    }
  },
  "id": 0
}
```

`wrangler pages deployment tail` 允许你自定义日志会话，以更好地满足你的需求。有关可用的配置选项，请参阅[`wrangler pages deployment tail` 文档](/workers/wrangler/commands/#deployment-tail)。

## 在 Cloudflare 控制面板中查看日志

要查看与任何部署相关的 "生产 "或 "预览 "环境的日志：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目，转到要查看日志的部署，然后选择 **查看详情**> **功能**。

记录功能适用于所有客户(免费、付费、企业)。

## Limits

以下限制适用于功能日志：

* 日志不存储。你可以随时启动和停止数据流以查看它们，但它们不会持久存在。
* 如果在过去 5 分钟内，功能的每秒请求数超过 100，日志将不会显示。
* 你的函数绑定的任何 [持久对象](/pages/functions/bindings/#durable-objects) 的日志都将显示在 Cloudflare 仪表板中。
* 一次最多可有 10 个客户端查看部署日志。这可以是仪表板会话或 `wrangler pages deployment tail`调用的组合。

## Sourcemaps

如果你正在调试未捕获的异常，你可能会发现日志中的[堆栈跟踪](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/stack)包含生成的 JavaScript 文件的行号。使用 Pages 对 [source maps](https://web.dev/articles/source-maps)的支持，你可以获得与原始源代码的行号和符号相匹配的堆栈跟踪。

{{<Aside type="note">}}

在开发全栈应用程序时，许多构建工具(包括用于 Pages Functions 的 wrangler 和大多数全栈框架)都会为客户端和服务器端生成源映射，请确保将你的构建步骤配置为仅生成服务器端源映射，或使用额外的构建步骤删除客户端源映射。公开源映射可能会向用户暴露应用程序的源代码。

{{</Aside>}}

请参阅 [源映射和堆栈跟踪](/pages/functions/source-maps/) 以获取更深入的解释。