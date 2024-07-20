---
pcx_content_type: how-to
title: 使用Pages Functions进行 A/B 测试
---

# 使用Pages Functions进行 A/B 测试

在本指南中，你将学习如何在 Pages 项目中使用 [Pages Functions](/pages/functions/) 进行 A/B 测试。A/B 测试是一种用户体验研究方法，用于比较网页或应用程序的两个或多个版本。通过 A/B 测试，你可以向用户提供两个或更多版本的网页，并划分网站流量。

## 概览

为 A/B 测试配置不同版本的应用程序将取决于你的具体使用情况。对于所有开发人员来说，A/B 测试设置可以简化为几个有用的原则。

根据应用程序版本的数量(本指南使用两个版本)，可以将用户分配到实验组。本指南中的实验组是基本路由 `/` 和测试路由 `/test`。

为确保用户留在你指定的组中，你将在浏览器中设置并存储 cookie，并根据你设置的 cookie 值提供相应的路径。

## 设置页面功能

在你的项目中，你可以使用 [Pages Functions](/pages/functions/) 处理 A/B 测试的逻辑。Pages Functions 允许你在 Pages 项目中处理服务器逻辑。

开始

1. 进入本地计算机上的 Pages 项目目录。
2. 创建一个 `/functions` 目录。应用服务器逻辑将位于`/functions`目录中。

## 添加中间件逻辑

页面函数(Pages Functions)具有实用功能，可以重复使用在路由处理程序之前和/或之后执行的逻辑块。这些函数称为 [中间件](/pages/functions/middleware/)。根据本指南，中间件可以让你在 Pages 项目的请求到达网站之前拦截它们。

在`/functions`目录下创建一个`_middleware.js`文件。

{{<Aside type="note">}}

在`/functions`文件夹下创建`_middleware.js`文件后，中间件将在项目的所有路由中运行。了解有关[中间件路由](/pages/functions/middleware/)的更多信息。

{{</Aside>}}

按照函数命名约定，`_middleware.js` 文件导出了一个单独的异步 `onRequest` 函数，该函数接受 `request`、`env` 和 `next` 作为参数。

```js
---
filename: /functions/_middleware.js
---
const abTest = async ({request, next, env}) => {
  /*
  Todo:
  1. Conditional statements to check for the cookie
  2. Assign cookies based on percentage, then serve
  */
}

export const onRequest = [abTest]
```

要设置 cookie，请创建 `cookieName` 变量并赋值。然后创建 `newHomepagePathName` 变量并将其赋值为 `/test`：

```js
---
filename: /functions/_middleware.js
highlight: [1,2]
---
const cookieName = "ab-test-cookie"
const newHomepagePathName = "/test"

const abTest = async ({request, next, env}) => {
  /*
  Todo:
  1. Conditional statements to check for the cookie
  2. Assign cookie based on percentage then serve
  */
}

export const onRequest = [abTest]
```

## 设置条件逻辑

根据 URL 路径名，检查 cookie 值是否等于`new`。如果值为  `new`，则将提供 `newHomepagePathName`。

```js
---
filename: /functions/_middleware.js
highlight: [7,8,9,10,11,12,13,14,15,16,17,18,19]
---
const cookieName = "ab-test-cookie"
const newHomepagePathName = "/test"

const abTest = async ({request, next, env}) => {
  /*
  Todo:
  1. Assign cookies based on randomly generated percentage, then serve
  */

  const url = new URL(request.url)
  if (url.pathname === "/") {
    // if cookie ab-test-cookie=new then change the request to go to /test
    // if no cookie set, pass x% of traffic and set a cookie value to "current" or "new"

    let cookie = request.headers.get("cookie")
    // is cookie set?
    if (cookie && cookie.includes(`${cookieName}=new`)) {
      // Change the request to go to /test (as set in the newHomepagePathName variable)
      url.pathname = newHomepagePathName
      return env.ASSETS.fetch(url)
    }
  }
}

export const onRequest = [abTest]
```

如果没有 cookie 值，则必须指定一个。使用以下方法生成一个百分比(从 0 到 99)：`Math.floor(Math.random() * 100)`。你的默认 cookie 版本值为 `current`。

如果生成数字的百分比低于 `50`，则将 cookie 版本指定为 `new`。根据随机生成的百分比，设置 cookie 并提供资产。在条件块之后，将请求传递给 `next()`。这将把请求传递给 Pages。这将导致 50% 的用户获得 `/test`主页。

通过 `env.ASSETS.fetch()` 函数，你可以将用户发送到通过 `url` 参数定义的修改路径。`env` 是包含环境变量和绑定的对象。`ASSETS` 是默认的函数绑定，允许在函数和 Pages 的资产服务资源之间进行通信。`fetch()`调用 Pages 的资产服务资源，并向网站访问者返回资产(`/test` 主页)。

{{<Aside type="note" header="Binding">}}

函数是在页面项目中执行的 Worker，用于添加动态功能。绑定是函数(Worker)与外部资源交互的方式。绑定是 Worker 运行时向代码提供的运行时变量。

{{</Aside>}}

```js
---
filename: /functions/_middleware.js
highlight: 20-36
---
const cookieName = "ab-test-cookie"
const newHomepagePathName = "/test"

const abTest = async (context) => {
  const url = new URL(context.request.url)
  // if homepage
  if (url.pathname === "/") {
    // if cookie ab-test-cookie=new then change the request to go to /test
    // if no cookie set, pass x% of traffic and set a cookie value to "current" or "new"

    let cookie = request.headers.get("cookie")
    // is cookie set?
    if (cookie && cookie.includes(`${cookieName}=new`)) {
      // pass the request to /test
      url.pathname = newHomepagePathName
      return context.env.ASSETS.fetch(url)
    } else {
      const percentage = Math.floor(Math.random() * 100)
      let version = "current" // default version
      // change pathname and version name for 50% of traffic
      if (percentage < 50) {
        url.pathname = newHomepagePathName
        version = "new"
      }
      // get the static file from ASSETS, and attach a cookie
      const asset = await context.env.ASSETS.fetch(url)
      let response = new Response(asset.body, asset)
      response.headers.append("Set-Cookie", `${cookieName}=${version}; path=/`)
      return response
    }
  }
  return context.next()
};

export const onRequest = [abTest];
```

## 部署到 Cloudflare Pages

在项目中设置好 `functions/_middleware.js` 文件后，就可以使用 Pages 进行部署了。将项目变更推送到 GitHub/GitLab。

部署应用程序后，请检查中间件功能：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在 "账户主页 "中，选择 "**工作者和页面**"。
3. 在**概览**中，选择页面项目 > **设置**> **功能**> **配置**。