---
pcx_content_type: how-to
title: 将 Worker 重构为 Pages Function
---

# 将 Worker 重构为 Pages Function

在本指南中，你将学习如何将用于接收表单提交的 Worker 重构为可托管在 Cloudflare Pages 应用程序上的 Pages 函数。[页面功能](/pages/functions/)是一种无服务器功能，与你的应用程序位于同一项目目录中，并与 Cloudflare Pages 一起部署。它使你能够运行添加动态功能的服务器端代码，而无需运行专用服务器。出于以下原因之一，你可能希望将 Worker 重构为 Pages Function：

1. 如果你要管理 Pages 应用程序所依赖的无服务器功能，并希望在不将 Worker 作为单独服务管理的情况下发送逻辑。
2. 如果你要将 Worker 迁移到 Pages Functions，并希望使用 Pages Functions 的路由和中间件功能。

{{<Aside type= "note">}}

你可以在 Pages 项目的输出目录中创建一个 `_worker.js` 文件，将 Worker 导入 Pages 项目，而无需使用 Functions。这种[高级模式](/pages/functions/advanced-mode/)要求使用[模块语法](/workers/reference/migrate-to-module-workers/)编写 Worker。

但是，在 Pages 中使用 `_worker.js` 文件时，整个 `/functions` 目录都会被忽略，包括其路由和中间件特性。

{{</Aside>}}

## 一般重构步骤

1. 删除 `addEventListener()` 方法及其事件响应，代之以适当的 `OnRequest` 方法。请参阅 [函数](/pages/functions/get-started/) 为你的函数选择合适的方法。
2. 将 `context` 对象作为参数传递给新的 `OnRequest` 方法，以访问上下文参数的属性：`request`、`env`、`params` 和 `next`。
3. 使用中间件处理必须在路由处理程序之前或之后执行的逻辑。有关 [使用中间件](/pages/functions/middleware/) 的更多信息，请参阅函数文档。

## 背景

为了解释重构过程，本指南使用了一个简单的表单提交示例。

表单提交可以由 Worker 处理，但也可以是页面函数的一个很好的用例，因为表单在大多数情况下是特定于某个应用程序的。

假设你已经使用 Worker 来处理表单，那么你应该已经部署了 Worker，然后将 URL 添加到 HTML 表单的表单操作属性中。这意味着，当你更改 Worker 处理提交的方式时，必须对 Worker 脚本进行更改。如果 Worker 中的逻辑被多个应用程序使用，那么页面功能就不是一个好的用例。

不过，当你希望在与应用程序相同的项目目录中组织函数逻辑时，使用 [Pages Function](/pages/functions/)可能会有所帮助。

使用 Pages Functions 构建应用程序可以帮助你从同一个地方管理客户端和无服务器逻辑，并使代码的编写和调试更加轻松。

## 使用 Airtable 和 Workers 处理表单条目

[Airtable](https://airtable.com/)是一个用于构建协作应用程序的低代码平台。它有助于自定义工作流程、协作和处理表单提交。在本例中，你将使用 Airtable 的表单提交功能。

[Airtable](https://airtable.com/) 可用于在同一账户的不同表格中存储信息条目。创建用于处理提交逻辑的 Worker 时，第一步是使用 [Wrangler](/workers/wrangler/install-and-update/)，在特定文件夹或应用程序根目录下初始化一个新 Worker。

本步骤创建模板，用于编写 Airtable 提交 Worker。编写 Worker 后，你可以在 [配置项目以便部署](/workers/wrangler/configuration/) 之后将其部署到 Cloudflare 的全球网络。有关如何 [使用 Worker 处理表单提交](/workers/tutorials/handle-form-submissions-with-airtable/) 的完整教程，请参阅 Worker 文档。

以下代码块显示了一个 Worker 的示例，该 Worker 可处理 Airtable 表单提交。

每个 Worker 都会对带有 `request` 处理程序的 `fetch` 操作做出默认响应。如果工作的路径名是 `/submit`，则会调用 `submitHandler` 异步函数。该函数会检查请求方法是否为 `POST` 请求，然后使用你的凭据(可使用 [Wrangler `secret`](/workers/wrangler/commands/#secret) 存储)解析表单条目并将其发布到 Airtable。

```js
---
filename: worker/index.js
---
addEventListener("fetch", (event) => {
  event.respondWith(handleRequest(event.request));
});


async function handleRequest(request) {
  const url = new URL(request.url);

  if (url.pathname === "/submit") {
    return submitHandler(request);
  }

  return fetch(request.url);
}

async function submitHandler(request) {
  if (request.method !== "POST") {
    return new Response("Method not allowed", {
      status: 405,
    });
  }
  const body = await request.formData();

  const { first_name, last_name, email, phone, subject, message } =
    Object.fromEntries(body);

  const reqBody = {
    fields: {
      "First Name": first_name,
      "Last Name": last_name,
      Email: email,
      "Phone number": phone,
      Subject: subject,
      Message: message,
    },
  };

  return HandleAirtableData(reqBody);
}

const HandleAirtableData = (body) => {
  return fetch(
    `https://api.airtable.com/v0/${AIRTABLE_BASE_ID}/${encodeURIComponent(
      AIRTABLE_TABLE_NAME
    )}`,
    {
      method: "POST",
      body: JSON.stringify(body),
      headers: {
        Authorization: `Bearer ${AIRTABLE_API_KEY}`,
        "Content-type": `application/json`,
      },
    }
  );
};
```


#### 重构你的 Worker

要重构上述 Worker，请进入 Pages 项目目录并创建一个 `/functions` 文件夹。在 `/functions`中，创建一个 `form.js` 文件。该文件将处理表单提交。

然后，在 `form.js` 文件中导出一个 `onRequestPost`：

```js
---
filename: functions/form.js
---
export async function onRequestPost(context) {
	return await submitHandler(context);
}

```

每个 Worker 都有一个 `addEventListener` 用于监听 `fetch` 事件，但在页面函数中并不需要。取而代之的是，你将 `export`一个单独的 `onRequest`函数，并根据其处理的 HTTPS 请求为其命名。请参考 [函数文档](/pages/functions/get-started/) 为你的函数选择合适的方法。

上述代码将 `request` 和 `env` 作为参数，并将这些属性传递给 `submitHandler` 函数，该函数与 [原始 Worker](#handle-form-entries-with-airtable-and-workers) 保持不变。不过，由于函数允许你指定 HTTPS 请求类型，你可以删除 Worker 中的 `request.method` 检查。现在，页面函数会通过命名 `onRequest` 处理程序来处理此问题。

现在，你将引入 `submitHandler` 函数，并将 `env` 参数作为属性传递。这将允许你在下面的 `HandleAirtableData` 函数中访问 `env`。该函数使用 Airtable 凭据向 Airtable 发送 `POST` 请求：

```js
---
filename: functions/form.js
highlight: [4,5,6,7,8,9,10,11,12,13,14,15,16,17,18,19,20,21,22]
---
export async function onRequestPost(context) {
	return await submitHandler(context);
}

async function submitHandler(context) {
  const body = await context.request.formData();

  const { first_name, last_name, email, phone, subject, message } =
    Object.fromEntries(body);

  const reqBody = {
    fields: {
      "First Name": first_name,
      "Last Name": last_name,
      Email: email,
      "Phone number": phone,
      Subject: subject,
      Message: message,
    },
  };

  return HandleAirtableData({ body: reqBody, env: env });
}
```

最后，创建一个 `HandleAirtableData` 函数。该函数将使用 Airtable 凭据和请求正文向 Airtable 发送 `fetch `请求：

```js
---
filename: functions/form.js
---

// ..
const HandleAirtableData = async function onRequest({ body, env }) {
  return fetch(
    `https://api.airtable.com/v0/${env.AIRTABLE_BASE_ID}/${encodeURIComponent(
      env.AIRTABLE_TABLE_NAME
    )}`,
    {
      method: "POST",
      body: JSON.stringify(body),
      headers: {
        Authorization: `Bearer ${env.AIRTABLE_API_KEY}`,
        "Content-type": `application/json`,
      },
    }
  );
};
```

你可以 [在本地使用 Wrangler](/pages/functions/local-development/) 测试你的函数。完成本指南后，你就成功地将表单提交 Worker 重构为表单提交页面函数。

## 相关资源

- [HTML 表格](/pages/tutorials/forms/)
- [插件文档](/pages/functions/plugins/)
- [函数文档](/pages/functions/)
