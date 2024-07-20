---
pcx_content_type: how-to
title: Bindings
weight: 7
---

# 绑定

[绑定](/workers/runtime-apis/bindings/) 使你的Pages Functions 能够与 Cloudflare 开发人员平台上的资源交互。使用绑定将你的 Pages 函数与 Cloudflare 资源(如 [KV](/kv/reference/how-kv-works/)、[Durable Objects](/durable-objects/)、[R2](/r2/) 和 [D1](/d1/))集成。你可以为生产环境和预览环境设置绑定。

本指南将指导你为页面功能配置绑定。你必须已经设置了 Cloudflare 开发者平台资源才能继续。


{{<Aside>}}

Pages Functions 只支持所有 [绑定](/workers/runtime-apis/bindings/) 的子集，这些子集已在本页列出。

{{</Aside>}}

## KV 命名空间

[Workers KV](/kv/reference/kv-namespaces/) 是 Cloudflare 的键值存储解决方案。

要将 KV 命名空间绑定到 Pages 函数，你可以在 [`wrangler.toml`](/pages/functions/wrangler-configuration/#kv-namespaces)或 Cloudflare 面板中配置 KV 命名空间绑定。

通过 Cloudflare 面板配置 KV 命名空间绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **功能**> **KV命名空间绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
6. 在**KV 命名空间**下，选择所需的命名空间。对于 **生产**和 **预览**环境，必须重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

下面是一个如何在函数中使用 KV 的示例。在下面的示例中，KV 命名空间绑定名为 `TODO_LIST`，你可以在 `context.env` 中访问 Function 代码中的绑定：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequest(context) {
  const task = await context.env.TODO_LIST.get("Task:123");
  return new Response(task);
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  TODO_LIST: KVNamespace;
}

export const onRequest: PagesFunction<Env> = async (context) => {
  const task = await context.env.TODO_LIST.get("Task:123");
  return new Response(task);
}
```
{{</tab>}}
{{</tabs>}}

### 在本地与 KV 命名空间交互

你可以通过两种方式之一在本地与 KV 命名空间绑定交互：

- 配置 Pages 项目的 `wrangler.toml` 文件并运行 [`npx wrangler pages dev`](/workers/wrangler/commands/#dev-1).
- 直接向 `wrangler pages dev` 传递参数。

要通过向 Wrangler CLI 传递参数与本地的 KV 命名空间绑定进行交互，请在 `wrangler pages dev` 命令中添加 `-k <BINDING_NAME>` 或 `--kv=<BINDING_NAME>` 。例如，如果你的 KV 命名空间通过 `TODO_LIST` 绑定与你的 Function 绑定，则在本地开发中通过运行以下命令访问 KV 命名空间：

```sh
$ npx wrangler pages dev <OUTPUT_DIR> --kv=TODO_LIST
```

{{<render file="_cli-precedence-over-file.md">}}

## Durable Objects

[耐用对象](/durable-objects/) (DO) 是 Cloudflare 的强一致性数据存储，可支持连接 WebSockets 和处理状态等功能。

{{<render file="_do-note.md" productFolder="pages">}}

要将耐用对象绑定到页面功能，你可以在 [`wrangler.toml`](/pages/functions/wrangler-configuration/#kv-namespaces)或 Cloudflare 面板中配置耐用对象绑定。

通过 Cloudflare 面板配置持久对象绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **功能**> **持久对象绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
6. 在**耐用对象命名空间**下，选择所需的命名空间。对于 **生产**和 **预览**环境，必须重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

下面是一个如何在函数中使用持久对象的示例。在下面的示例中，你的 DO 绑定名为 `DURABLE_OBJECT`，你可以在函数代码的 `context.env` 中访问该绑定：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequestGet(context) {
  const id = context.env.DURABLE_OBJECT.newUniqueId();
  const stub = context.env.DURABLE_OBJECT.get(id);

  // Pass the request down to the durable object
  return stub.fetch(context.request);
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  DURABLE_OBJECT: DurableObjectNamespace;
}

export const onRequestGet: PagesFunction<Env> = async (context) => {
  const id = context.env.DURABLE_OBJECT.newUniqueId();
  const stub = context.env.DURABLE_OBJECT.get(id);

  // Pass the request down to the durable object
  return stub.fetch(context.request);
}
```
{{</tab>}}
{{</tabs>}}

### 在本地与耐用对象命名空间交互

你可以通过两种方式之一与本地的持久对象绑定进行交互：

- 配置 Pages 项目的 `wrangler.toml` 文件并运行 [`npx wrangler pages dev`](/workers/wrangler/commands/#dev-1).
- 直接向 `wrangler pages dev` 传递参数。

在本地开发时，要与持久对象命名空间交互，请在导出持久对象的 Worker 目录中运行 `wrangler dev`。在另一个终端，在 Pages 项目目录下运行 `wrangler pages dev`。

要通过 Wrangler CLI 在本地与耐用对象命名空间交互，请在 `wrangler pages dev` 中添加 `--do <BINDING_NAME>=<CLASS_NAME>@<SCRIPT_NAME>` 。`CLASS_NAME `表示 Durable Object 的类名，`SRIPT_NAME `表示 Worker 的名称。

例如，如果你的 Worker 名为 `do-worker`，并且声明了一个名为 `DurableObjectExample` 的持久对象类，那么在 `do-worker` 目录中运行 `npx wrangler dev` 即可访问该持久对象。同时，在 Pages 的项目目录中运行 `npx wrangler pages dev <OUTPUT_DIR> --do MY_DO=DurableObjectExample@do-worker` 。使用 `context.env`(例如，`context.env.MY_DO`)与功能代码中的 `MY_DO` 绑定进行交互。

{{<render file="_cli-precedence-over-file.md">}}

## R2 桶

[R2](/r2/) 是 Cloudflare 的 blob 存储解决方案，允许开发人员存储大量非结构化数据，而无需支付出口费用。

要将 R2 桶绑定到 Pages 功能，你可以在 [`wrangler.toml`](/pages/functions/wrangler-configuration/#r2-buckets)或 Cloudflare 面板中配置 R2 桶绑定。

要通过 Cloudflare 面板配置 R2 数据桶绑定，请执行以下操作

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **功能**> **R2桶绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
6. 在 **R2存储桶**下，选择所需的 R2 存储桶。对于 **生产**和 **预览**环境，必须重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

下面是一个如何在函数中使用 R2 存储桶的示例。在下面的示例中，R2 数据桶绑定名为 `BUCKET`，你可以在 `context.env` 中访问函数代码中的绑定：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequest(context) {
  const obj = await context.env.BUCKET.get('some-key');
  if (obj === null) {
    return new Response('Not found', { status: 404 });
  }
  return new Response(obj.body);
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  BUCKET: R2Bucket;
}

export const onRequest: PagesFunction<Env> = async (context) => {
  const obj = await context.env.BUCKET.get('some-key');
  if (obj === null) {
    return new Response('Not found', { status: 404 });
  }
  return new Response(obj.body);
}
```
{{</tab>}}
{{</tabs>}}

### 在本地与 R2 存储桶交互

你可以通过两种方式之一在本地与 R2 水桶绑定进行交互：

- 配置 Pages 项目的 `wrangler.toml` 文件并运行 [`npx wrangler pages dev`](/workers/wrangler/commands/#dev-1).
- 直接向 `wrangler pages dev` 传递参数。

{{<Aside type="note">}}

默认情况下，Wrangler 会自动将数据持久化到本地存储。更多信息，请参阅 [本地开发](/workers/testing/local-development/)。

{{</Aside>}}

要通过 Wrangler CLI 在本地与 R2 数据桶交互，请在 `wrangler pages dev` 命令中添加 `--r2=<BINDING_NAME>`。如果你的 R2 源代码桶已通过 `BUCKET` 绑定与你的 Function 绑定，则可在本地开发中通过运行以下命令访问该 R2 存储桶：

```sh
$ npx wrangler pages dev <OUTPUT_DIR> --r2=BUCKET
```

使用 `context.env`(例如 `context.env.BUCKET`)与此绑定进行交互。

{{<render file="_cli-precedence-over-file.md">}}

## D1 数据库

[D1](/d1/) 是 Cloudflare 的本地无服务器数据库。

要将 D1 数据库绑定到你的页面功能，你可以在 [`wrangler.toml`](/pages/functions/wrangler-configuration/#d1-databases)或 Cloudflare 面板中配置 D1 数据库绑定。

通过 Cloudflare 面板配置 D1 数据库绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **功能**> **D1数据库绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
6. 在**D1 数据库**下，选择所需的 D1 数据库。对于 **生产**和 **预览**环境，必须重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

下面是如何在函数中使用 D1 的示例。在下面的示例中，你的 D1 数据库绑定是 `NORTHWIND_DB`，你可以在 `context.env` 中访问 Function 代码中的绑定：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequest(context) {
  // Create a prepared statement with our query
  const ps = context.env.NORTHWIND_DB.prepare('SELECT * from users');
  const data = await ps.first();

  return Response.json(data);
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  NORTHWIND_DB: D1Database;
}

export const onRequest: PagesFunction<Env> = async (context) => {
  // Create a prepared statement with our query
  const ps = context.env.NORTHWIND_DB.prepare('SELECT * from users');
  const data = await ps.first();

  return Response.json(data);
}
```
{{</tab>}}
{{</tabs>}}

### 在本地与 D1 数据库交互

你可以通过两种方式之一在本地与 D1 数据库绑定交互：

- 配置 Pages 项目的 `wrangler.toml` 文件并运行 [`npx wrangler pages dev`](/workers/wrangler/commands/#dev-1).
- 直接向 `wrangler pages dev` 传递参数。

要在[本地开发](/d1/build-with-d1/local-development/#develop-locally-with-pages)时通过 Wrangler CLI 与 D1 数据库交互，请在 `wrangler pages dev `命令中添加`--d1 <BINDING_NAME>=<DATABASE_ID>` 。

如果 D1 数据库通过 `NORTHWIND_DB` 绑定到了 Pages Function，且 `wrangler.toml` 文件中的 `database_id` 为 `xxxx-xxxx-xxxx-xxxx-xxxx`，则可在本地开发中通过运行 `Wrangler.toml` 访问该数据库：

```sh
$ npx wrangler pages dev <OUTPUT_DIR> --d1 NORTHWIND_DB=xxxx-xxxx-xxxx-xxxx-xxxx
```

通过使用 `context.env`(例如 `context.env.NORTHWIND_DB`)与此绑定进行交互。

{{<Aside type="note">}}

默认情况下，Wrangler 会自动将数据持久化到本地存储。更多信息，请参阅 [本地开发](/workers/testing/local-development/)。

{{</Aside>}}

有关 D1 绑定上可用的 API 方法，请参阅 [D1 客户端 API 文档](/d1/build-with-d1/d1-client-api/)。

{{<render file="_cli-precedence-over-file.md">}}

## 将索引矢量化

[Vectorize](/vectorize/) 是 Cloudflare 的本地矢量数据库。

要将 Vectorize 索引绑定到页面功能，可在 [`wrangler.toml`](/pages/functions/wrangler-configuration/#vectorize-indexes)或 Cloudflare 面板中配置 Vectorize 索引绑定。

通过 Cloudflare 面板配置 Vectorize 索引绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择是在**生产**环境还是**预览**环境中设置绑定。
4. 选择页面项目 > **设置**> **功能**> **矢量化索引绑定**> **添加绑定**。
5. 在**变量名**下为绑定命名。
6. 在**矢量化索引**下，选择所需的矢量化索引。你必须对 **生产**和 **预览**环境重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

### 使用矢量化索引绑定

要在Pages Functions 中使用 Vectorize 索引，可以在Pages Functions 代码中访问 Vectorize 索引绑定。在下面的示例中，你的 Vectorize 索引绑定名为 `VECTORIZE_INDEX`，你可以在Pages Functions 代码中的 `context.env` 中访问该绑定。


{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
// Sample vectors: 3 dimensions wide.
//
// Vectors from a machine-learning model are typically ~100 to 1536 dimensions
// wide (or wider still).
const sampleVectors = [
	{ id: '1', values: [32.4, 74.1, 3.2], metadata: { url: '/products/sku/13913913' } },
	{ id: '2', values: [15.1, 19.2, 15.8], metadata: { url: '/products/sku/10148191' } },
	{ id: '3', values: [0.16, 1.2, 3.8], metadata: { url: '/products/sku/97913813' } },
	{ id: '4', values: [75.1, 67.1, 29.9], metadata: { url: '/products/sku/418313' } },
	{ id: '5', values: [58.8, 6.7, 3.4], metadata: { url: '/products/sku/55519183' } },
];

export async function onRequest(context) {
  let path = new URL(context.request.url).pathname;
  if (path.startsWith("/favicon")) {
    return new Response('', { status: 404 });
  }

  // You only need to insert vectors into your index once
  if (path.startsWith("/insert")) {
    // Insert some sample vectors into your index
    // In a real application, these vectors would be the output of a machine learning (ML) model,
    // such as Workers AI, OpenAI, or Cohere.
    let inserted = await context.env.VECTORIZE_INDEX.insert(sampleVectors);

    // Return the number of IDs we successfully inserted
    return Response.json(inserted);
  }
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
export interface Env {
	// This makes our vector index methods available on context.env.VECTORIZE_INDEX.*
	// For example, context.env.VECTORIZE_INDEX.insert() or query()
	VECTORIZE_INDEX: VectorizeIndex;
}

// Sample vectors: 3 dimensions wide.
//
// Vectors from a machine-learning model are typically ~100 to 1536 dimensions
// wide (or wider still).
const sampleVectors: Array<VectorizeVector> = [
	{ id: '1', values: [32.4, 74.1, 3.2], metadata: { url: '/products/sku/13913913' } },
	{ id: '2', values: [15.1, 19.2, 15.8], metadata: { url: '/products/sku/10148191' } },
	{ id: '3', values: [0.16, 1.2, 3.8], metadata: { url: '/products/sku/97913813' } },
	{ id: '4', values: [75.1, 67.1, 29.9], metadata: { url: '/products/sku/418313' } },
	{ id: '5', values: [58.8, 6.7, 3.4], metadata: { url: '/products/sku/55519183' } },
];

export const onRequest: PagesFunction<Env> = async (context) => {
  let path = new URL(context.request.url).pathname;
  if (path.startsWith("/favicon")) {
    return new Response('', { status: 404 });
  }

  // You only need to insert vectors into your index once
  if (path.startsWith("/insert")) {
    // Insert some sample vectors into your index
    // In a real application, these vectors would be the output of a machine learning (ML) model,
    // such as Workers AI, OpenAI, or Cohere.
    let inserted = await context.env.VECTORIZE_INDEX.insert(sampleVectors);

    // Return the number of IDs we successfully inserted
    return Response.json(inserted);
  }
}
```
{{</tab>}}
{{</tabs>}}

## Works AI

[Workers AI](/workers-ai/)允许你在 Cloudflare 的全球网络上运行由无服务器 GPU 驱动的机器学习模型。

要将 Workers AI 绑定到你的页面功能，你可以在 [`wrangler.toml`](/pages/functions/wrangler-configuration/#workers-ai)或 Cloudflare 面板中配置 Workers AI 绑定。

使用 Wrangler 进行本地开发时，可以使用 `--ai` 标志定义 AI 绑定。在开发模式下运行 [`wrangler pages dev --ai AI`](/workers/wrangler/commands/#dev) 启动 Wrangler，以公开 `context.env.AI` 绑定。

要通过 Cloudflare 控制面板配置 Workers AI 绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **功能**> **工作程序 AI 绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
7. 重新部署项目，使绑定生效。

### 使用Works AI绑定

要在页面功能中使用 Workers AI，你可以在页面功能代码中访问 Workers AI 绑定。在下面的示例中，Workers AI 绑定名为 `AI`，你可以在页面功能代码的 `context.env `中访问该绑定。

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequest(context) {
  const input = { prompt: "What is the origin of the phrase Hello, World" }

  const answer = await context.env.AI.run('@cf/meta/llama-3-8b-instruct', input);

  return Response.json(answer);
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  AI: Ai;
}

export const onRequest: PagesFunction<Env> = async (context) => {
  const input = { prompt: "What is the origin of the phrase Hello, World" }

  const answer = await context.env.AI.run('@cf/meta/llama-3-8b-instruct', input)

  return Response.json(answer);
}
```
{{</tab>}}
{{</tabs>}}

### 在本地与你的Works AI绑定互动

{{<render file="_ai-local-usage-charges.md" productFolder="workers">}}

你可以通过两种方式之一在本地与Works AI绑定进行交互：

- 配置 Pages 项目的 `wrangler.toml` 文件并运行 [`npx wrangler pages dev`](/workers/wrangler/commands/#dev-1).
- 直接向 `wrangler pages dev` 传递参数。

要在本地开发时通过 Wrangler CLI 与 Workers AI 绑定交互，请运行

```sh
$ npx wrangler pages dev --ai=<BINDING_NAME>
```

{{<render file="_cli-precedence-over-file.md">}}

## 服务绑定

[服务绑定](/workers/runtime-apis/bindings/service-bindings/) 使你可以在Pages Function中调用 Worker。

要将你的页面功能绑定到 Worker，请使用 [`wrangler.toml`](/pages/functions/wrangler-configuration/#service-bindings) 或 Cloudflare 面板在你的页面功能中配置服务绑定。

通过 Cloudflare 面板配置服务绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **功能**> **服务绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
6. 在**服务**下，选择所需Works。你必须对 **生产**和 **预览**环境重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

下面是一个如何在函数中使用服务绑定的示例。在下面的示例中，服务绑定名为 `SERVICE`，你可以在 `context.env` 中访问函数代码中的绑定：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequestGet(context) {
  return context.env.SERVICE.fetch(context.request);
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  SERVICE: Fetcher;
}

export const onRequest: PagesFunction<Env> = async (context) => {
  return context.env.SERVICE.fetch(context.request);
}
```
{{</tab>}}
{{</tabs>}}

### 与本地服务绑定互动

你可以通过两种方式之一在本地与服务绑定交互：

- 配置 Pages 项目的 `wrangler.toml` 文件并运行 [`npx wrangler pages dev`](/workers/wrangler/commands/#dev-1).
- 直接向 `wrangler pages dev` 传递参数。

要在本地开发时与[服务绑定](/workers/runtime-apis/bindings/service-bindings/)进行交互，请通过`wrangler dev`运行你要绑定的 Worker，同时运行`wrangler pages dev`，并在其中加入`--service <BINDING_NAME>=<SRIPT_NAME>`，其中`SRIPT_NAME`表示 Worker 的名称。例如，如果 Worker 的名称是 `my-worker`，则通过 `npx wrangler dev`(在 Worker 的目录下)与 `npx wrangler pages dev <OUTPUT_DIR> --service MY_SERVICE=my-worker`(在 Pages 的目录下)同时运行来连接该 Worker。使用 `context.env`(例如 `context.env.MY_SERVICE`)与此绑定交互。

如果通过 Cloudflare 控制面板设置服务绑定，则需要在`wrangler pages dev` 后添加`--service <BINDING_NAME>=<SRIPT_NAME>`，其中`BINDING_NAME`是服务绑定的名称，`SRIPT_NAME`是 Worker 的名称。

例如，要进行本地开发，如果 Worker 的名称是 `my-worker`，则在 `my-worker `目录下运行 `npx wrangler dev`。在另一个终端，也在 Pages 项目目录下运行 `npx wrangler pages dev <OUTPUT_DIR> --service MY_SERVICE=my-worker`。使用 `context.env`(例如，`context.env.MY_SERVICE`)与此服务绑定交互。

{{<render file="_cli-precedence-over-file.md">}}

## 队列生产者

[队列生产者](/queues/configuration/javascript-apis/#producer) 使你能够在页面功能中将消息发送到队列中。

要将队列绑定到你的页面功能，请使用 [`wrangler.toml`](/pages/functions/wrangler-configuration/#queues-producers)或 Cloudflare 面板在你的页面功能中配置队列生产者绑定：

通过 Cloudflare 面板配置队列生产者绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择你的 Pages 项目 > **设置**> **功能**> **队列生成器绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
6. 在**数据集**下，输入所需的数据集。对于 **生产**和 **预览**环境，你必须重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

下面是一个如何在函数中使用队列生产者绑定的示例。在此示例中，绑定名为 `MY_QUEUE`，你可以在函数代码的 `context.env`: 中访问绑定：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequest(context) {
  await context.env.MY_QUEUE.send({
    url: request.url,
    method: request.method,
    headers: Object.fromEntries(request.headers),
  });

  return new Response('Sent!');
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  MY_QUEUE: Queue<any>;
}

export const onRequest: PagesFunction<Env> = async (context) => {
  await context.env.MY_QUEUE.send({
    url: request.url,
    method: request.method,
    headers: Object.fromEntries(request.headers),
  });

  return new Response('Sent!');
}
```
{{</tab>}}
{{</tabs>}}

### 在本地与队列生产者绑定互动

如果将队列生产者绑定到 Pages Function，就可以在本地向队列发送事件。但是，无法使用Pages Functions 从队列中消费事件。你必须创建一个[单独的消费者 Worker](/queues/get-started/#5-create-your-consumer-worker)和一个[队列消费者处理程序](/queues/configuration/javascript-apis/#consumer)，才能从队列中消费事件。Wrangler 尚不支持在本地分别运行绑定到同一队列的生产者函数和消费者 Worker。

## 分析引擎

[分析引擎](/analytics/analytics-engine/) 绑定可让你在页面功能中编写分析。

要将分析引擎数据集绑定到你的页面功能，必须使用 [`wrangler.toml`](/pages/functions/wrangler-configuration/#analytics-engine-datasets)或 Cloudflare 面板配置分析引擎绑定：

通过 Cloudflare 面板配置分析引擎绑定：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **功能**> **分析引擎绑定**> **添加绑定**。
4. 选择是在**生产**环境还是**预览**环境中设置装订。
5. 在**变量名**下为绑定命名。
6. 在**数据集**下，输入所需的数据集。对于 **生产**和 **预览**环境，你必须重复步骤 5 和 6。
7. 重新部署项目，使绑定生效。

下面是如何在函数中使用分析引擎绑定的示例。在下面的示例中，绑定名为 `ANALYTICS_ENGINE`，你可以在 `context.env `中访问函数代码中的绑定：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export async function onRequest(context) {
  const url = new URL(context.request.url);

  context.env.ANALYTICS_ENGINE.writeDataPoint({
    indexes: [],
    blobs: [url.hostname, url.pathname],
    doubles: [],
  });

  return new Response('Logged analytic');
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  ANALYTICS_ENGINE: AnalyticsEngineDataset;
}

export const onRequest: PagesFunction<Env> = async (context) => {
  const url = new URL(context.request.url);

  context.env.ANALYTICS_ENGINE.writeDataPoint({
    indexes: [],
    blobs: [url.hostname, url.pathname],
    doubles: [],
  });

  return new Response('Logged analytic');
}
```
{{</tab>}}
{{</tabs>}}

### 在本地与分析引擎绑定互动

无法在本地使用分析引擎绑定。

## 环境变量

[环境变量](/workers/configuration/environment-variables/) 是一个注入值，可由你的函数访问。环境变量是一种绑定类型，可将文本字符串或 JSON 值附加到 Pages 函数。它以纯文本形式存储。在运行时和构建时，直接在 Cloudflare 面板中为生产环境和预览环境设置环境变量。

要在 Pages 项目中添加环境变量，可使用 [`wrangler.toml`](/pages/functions/wrangler-configuration/#environment-variables)或 Cloudflare 面板。

要通过 Cloudflare 面板配置环境变量：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > **设置**> **环境变量**。
4. 在**制作**和/或**预览**下选择**添加变量**。
6. 设置变量名和变量值后，选择**保存**。

下面是一个如何在函数中使用环境变量的示例。本例中的环境变量是 `ENVIRONMENT`，你可以通过 `context.env`访问该环境变量：

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
export function onRequest(context) {
	if (context.env.ENVIRONMENT === 'development') {
		return new Response('This is a local environment!');
	} else {
		return new Response('This is a live environment');
	}
}
```
{{</tab>}}
{{<tab label="ts">}}
```ts
interface Env {
  ENVIRONMENT: string;
}

export const onRequest: PagesFunction<Env> = async (context) => {
	if (context.env.ENVIRONMENT === 'development') {
		return new Response('This is a local environment!');
	} else {
		return new Response('This is a live environment');
	}
}
```
{{</tab>}}
{{</tabs>}}

### 与本地环境变量互动

你可以通过两种方式之一在本地与环境变量交互：

- 配置 Pages 项目的 `wrangler.toml` 文件并运行 `npx wrangler pages dev`。
- 直接向 [`wrangler pages dev`](/workers/wrangler/commands/#dev-1) 传递参数。

要通过 Wrangler CLI 在本地与环境变量交互，请在 `wrangler pages dev` 命令中添加 `-binding=<ENVIRONMENT_VARIABLE_NAME>=<ENVIRONMENT_VARIABLE_VALUE>`：

```sh
$ npx wrangler pages dev --binding=<ENVIRONMENT_VARIABLE_NAME>=<ENVIRONMENT_VARIABLE_VALUE>
```

## Secrets

Secrets是一种绑定类型，允许你为Pages Functions 附加加密文本值。设置Secrets后，你将无法看到Secrets，只能在 `context.env`上以编程方式访问Secrets。Secrets用于存储敏感信息，如 API 密钥和授权令牌。

要在 Pages 项目中添加Secrets：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择页面项目 > 选择 **设置**> **环境变量**。
4. 在**制作**和/或**预览**下选择**添加变量**。
5. 选择**加密**，创建你的Secrets。
6. 选择 **保存**。

Secrets的使用方法与环境变量相同。使用 Wrangler 或在 Cloudflare 面板中设置Secrets时，需要在使用这些Secrets的部署之前完成。有关更多指导，请参阅 [环境变量](#environment-variables)。

#### 在本地与你的Secrets互动

本地开发时，在 Pages 项目的根目录下创建一个 `.dev.vars` 文件来添加Secrets。然后将以下代码段添加到 `.dev.vars`：

```
---
filename:  `.dev.vars`
---
SECRET_NAME=<SECRET_VALUE>
```