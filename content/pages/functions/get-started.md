---
pcx_content_type: get-started
title: 开始
weight: 1
meta:
  title: Functions - Get started
---

# 开始

本指南将指导你创建和部署Pages Function。

## 先决条件

你必须在本地计算机上设置一个 Pages 项目或在 Cloudflare 控制面板上部署一个 Pages 项目。要创建 Pages 项目，请参阅 [开始](/pages/get-started/)。

## 创建一个函数

要开始生成 Pages 函数，请在 Pages 项目根目录下创建一个 `/functions` 目录。

{{<Aside type="note" header="Advanced mode">}}

对于不需要 Pages Functions 内置的基于文件路径的路由和中间件系统的现有应用程序，请使用[高级模式](/pages/functions/advanced-mode/)。高级模式允许你使用`_worker.js `文件而不是`/functions `目录开发 Pages Functions。

{{</Aside>}}

将 Functions 文件写入`/functions`目录，就会在预先指定的路线上自动生成具有自定义功能的 Worker。

将以下代码复制并粘贴到你在`/functions`文件夹下创建的`helloworld.js`文件中：

```js
---
filename: helloworld.js
---
export function onRequest(context) {
  return new Response("Hello, world!")
}
```

在上述示例代码中，`onRequest `处理程序接收一个请求 [`context`](/pages/functions/api-reference/#eventcontext)对象。处理程序必须返回一个 `Response` 或一个 `Response` 的 `Promise`。

此函数将在 `/helloworld` 路由上运行，并返回 `Hello, world!`。此函数在此路由上可用的原因是文件名为 `helloworld.js`。同样，如果该文件名为 `howdyworld.js`，则此函数将在 `/howdyworld`上运行。

有关路由自定义的更多信息，请参阅 [路由](/页面/功能/路由/)。

### 运行时功能

Pages Function可配置 Workers 运行时功能，包括与 Node.js API 子集兼容和设置[兼容日期或兼容标志](/workers/configuration/compatibility-dates/)。

向 [Wrangler](/workers/wrangler/commands/#dev-1) 命令传递参数或在仪表板中设置这些配置。在 Cloudflare 面板中设置页面兼容性标志：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 选择 **工作者和页面**，然后选择页面项目。
3. 选择 **设置**> **功能**> **兼容性标志**。
4. 根据需要配置生产和预览兼容性标志。

此外，通过配置 [绑定](/pages/functions/bindings/)，还可在 Pages 项目中使用其他 Cloudflare 产品，如 [D1](/d1/) (无服务器数据库)和 [R2](/r2/)。

## 部署你的功能

设置好功能后，部署 Pages 项目。通过以下方式部署项目

* 连接你的 [Git 提供商](/pages/get-started/git-integration/)。
* 从命令行使用 [Wrangler](/workers/wrangler/commands/#pages)。

{{<Aside type="warning">}}
Cloudflare 仪表板上的[直接上传](/pages/get-started/direct-upload/) 功能目前不支持。
{{</Aside>}}

## 相关资源

- 自定义你的[功能路由](/pages/functions/routing/)
- 查看[应用程序接口参考](/pages/functions/api-reference/)
- 学习如何 [调试你的函数](/pages/functions/debugging-and-logging/)