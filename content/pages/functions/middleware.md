---
pcx_content_type: how-to
title: 中间件
weight: 5
---

# 中间件

中间件是可在 [`onRequest`](/pages/functions/api-reference/#onrequests)函数之前运行的可重用逻辑。中间件通常是实用功能。错误处理、用户身份验证和日志记录是应用程序中典型的中间件。

## 添加中间件

中间件与标准 Pages 函数类似，但中间件总是定义在项目`/functions`目录下的`_middleware.js`文件中。`_middleware.js` 文件导出了一个 [`onRequest`](/pages/functions/api-reference/#onrequests) 函数。中间件将在与同一 `/functions`(包括子目录)目录下的任何 Pages Functions 相匹配的请求上运行。例如，`functions/users/_middleware.js` 文件将匹配`/functions/users/nevi`、`/functions/users/nevi/123`和`functions/users`的请求。

如果要在整个应用程序(包括静态文件)中运行中间件，请创建一个 `functions/_middleware.js` 文件。

在 `_middleware.js` 文件中，你可以导出一个 `onRequest` 处理程序或其任何特定于方法的变体。下面是一个中间件示例，用于处理项目的页面函数中抛出的任何错误。此示例使用了请求处理程序上下文对象中可用的 `next()` 方法：

```js
---
filename: functions/_middleware.js
---
export async function onRequest(context) {
  try {
    return await context.next();
  } catch (err) {
    return new Response(`${err.message}\n${err.stack}`, { status: 500 });
  }
}
```

## 链式中间件

你可以导出 Pages 函数数组作为中间件处理程序。这样，你就可以将要运行的多个中间件串联起来。在下面的示例中，你可以处理项目函数生成的任何错误，并检查用户是否通过身份验证：

```js
---
filename: functions/_middleware.js
---
async function errorHandling(context) {
  try {
    return await context.next();
  } catch (err) {
    return new Response(`${err.message}\n${err.stack}`, { status: 500 });
  }
}

function authentication(context) {
  if (context.request.headers.get("x-email") != "admin@example.com") {
    return new Response("Unauthorized", { status: 403 });
  }

  return context.next();
}

export const onRequest = [errorHandling, authentication];
```

在上述示例中，`errorHandling `函数将首先运行。它将捕获 `authentication` 函数中的任何错误以及任何其他后续页面函数中的错误。