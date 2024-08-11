---
pcx_content_type: configuration
title: API
weight: 2
meta:
  description: A set of programmatic APIs that can be integrated with local Cloudflare Workers-related workflows.
---

# Wrangler API

Wrangler 提供 API，以编程方式与 Cloudflare Workers 交互。

- [`unstable_dev`](#unstable_dev) - 启动服务器，针对 Worker 运行端到端(e2e)测试或集成测试。
- [`getPlatformProxy`](#getplatformproxy) - 在 Node.js 进程中获取用于模拟 Cloudflare Workers 平台的代理和值。

## `unstable_dev`

启动 HTTP 服务器，测试 Worker。

调用后，`unstable_dev` 将返回一个`fetch()`函数，用于调用 Worker 而无需知道地址或端口，以及一个`stop()`函数，用于关闭 HTTP 服务器。

默认情况下，`unstable_dev` 将针对本地服务器进行集成测试。如果希望针对预览 Worker 执行 e2e 测试，请在调用 `unstable_dev()` 函数时在 `options` 对象中传递 `local: false` 。请注意，e2e 测试可能比集成测试慢得多。

{{<Aside type="note">}}

`unstable_dev()`函数的前缀是`unstable_`，因为 API 是试验性的，将来可能会发生变化。

`unstable_dev()` 没有已知错误，可以安全使用。如果发现任何错误，请打开 [GitHub issue](https://github.com/cloudflare/workers-sdk/issues/new/choose).

{{</Aside>}}

### 构造函数

```js
const worker = await unstable_dev(script, options)
```

### 参数

{{<definitions>}}

*   `script` {{<type>}}string{{</type>}}

    * 包含 Worker 脚本路径的字符串，相对于 Worker 项目的根目录。

*   `options` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

    * 包含 `wrangler dev` 配置设置的可选选项对象。
    * 在 `options` 中包含一个 `experimental` 对象，以访问 `disableExperimentalWarning` 等实验功能。
        * 将 `disableExperimentalWarning` 设为 `true`，以禁用 Wrangler 对使用 `unstable_` 前缀 API 的警告。

{{</definitions>}}

### 返回类型

`unstable_dev()` 返回一个包含以下方法的对象：

{{<definitions>}}

*   `fetch()` {{<type>}}Promise\<Response>{{</type>}}

    * 向 Worker 发送请求。返回一个与 [`Response`](/workers/runtime-apis/response)对象解析的 Promise。
    * 请参阅 [`Fetch`](/workers/runtime-apis/fetch/)。


*   `stop()` {{<type>}}Promise\<void>{{</type>}}

    * 关闭开发服务器。

{{</definitions>}}

### 使用方法

启动每个测试套件时，使用 `beforeAll()` 函数启动 `unstable_dev()`。使用 `beforeAll()` 函数是为了最大限度地减少开销：启动开发服务器只需几百毫秒，而每个测试的启动和停止时间会迅速增加，从而减慢测试速度。

在每个测试用例中，调用 `await worker.fetch()`，并检查响应是否与预期一致。

要结束测试套件，可在`afterAll`函数中调用`await worker.stop()`。

#### 单个Works示例

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
---
filename: src/index.test.js
---
const { unstable_dev } = require("wrangler");

describe("Worker", () => {
  let worker;

  beforeAll(async () => {
    worker = await unstable_dev("src/index.js", {
      experimental: { disableExperimentalWarning: true },
    });
  });

  afterAll(async () => {
    await worker.stop();
  });

  it("should return Hello World", async () => {
    const resp = await worker.fetch();
    const text = await resp.text();
    expect(text).toMatchInlineSnapshot(`"Hello World!"`);
  });
});
```
{{</tab>}}
{{<tab label="ts" >}}
```ts
---
filename: src/index.test.ts
---
import { unstable_dev } from "wrangler";
import type { UnstableDevWorker } from "wrangler";

describe("Worker", () => {
  let worker: UnstableDevWorker;

  beforeAll(async () => {
    worker = await unstable_dev("src/index.ts", {
      experimental: { disableExperimentalWarning: true },
    });
  });

  afterAll(async () => {
    await worker.stop();
  });

  it("should return Hello World", async () => {
    const resp = await worker.fetch();
    const text = await resp.text();
    expect(text).toMatchInlineSnapshot(`"Hello World!"`);
  });
});
```
{{</tab>}}
{{</tabs>}}


#### 多Worker示例

你可以测试调用其他 Worker 的 Worker。在下面的示例中，我们将调用其他 Worker 的 Worker 称为父 Worker，将被调用的 Worker 称为子 Worker。

如果你过早关闭子 Worker，父 Worker 将不知道子 Worker 的存在，你的测试就会失败。

{{<tabs labels="js | ts">}}
{{<tab label="js" default="true">}}
```js
---
filename: src/index.test.js
---
import { unstable_dev } from "wrangler";

describe("multi-worker testing", () => {
  let childWorker;
  let parentWorker;

  beforeAll(async () => {
    childWorker = await unstable_dev("src/child-worker.js", {
      config: "src/child-wrangler.toml",
      experimental: { disableExperimentalWarning: true },
    });
    parentWorker = await unstable_dev("src/parent-worker.js", {
      config: "src/parent-wrangler.toml",
      experimental: { disableExperimentalWarning: true },
    });
  });

  afterAll(async () => {
    await childWorker.stop();
    await parentWorker.stop();
  });

  it("childWorker should return Hello World itself", async () => {
    const resp = await childWorker.fetch();
    const text = await resp.text();
    expect(text).toMatchInlineSnapshot(`"Hello World!"`);
  });

  it("parentWorker should return Hello World by invoking the child worker", async () => {
    const resp = await parentWorker.fetch();
    const parsedResp = await resp.text();
    expect(parsedResp).toEqual("Parent worker sees: Hello World!");
  });
});
```
{{</tab>}}
{{<tab label="ts" >}}
```ts
---
filename: src/index.test.ts
---
import { unstable_dev } from "wrangler";
import type { UnstableDevWorker } from "wrangler";

describe("multi-worker testing", () => {
  let childWorker: UnstableDevWorker;
  let parentWorker: UnstableDevWorker;

  beforeAll(async () => {
    childWorker = await unstable_dev("src/child-worker.js", {
      config: "src/child-wrangler.toml",
      experimental: { disableExperimentalWarning: true },
    });
    parentWorker = await unstable_dev("src/parent-worker.js", {
      config: "src/parent-wrangler.toml",
      experimental: { disableExperimentalWarning: true },
    });
  });

  afterAll(async () => {
    await childWorker.stop();
    await parentWorker.stop();
  });

  it("childWorker should return Hello World itself", async () => {
    const resp = await childWorker.fetch();
    const text = await resp.text();
    expect(text).toMatchInlineSnapshot(`"Hello World!"`);
  });

  it("parentWorker should return Hello World by invoking the child worker", async () => {
    const resp = await parentWorker.fetch();
    const parsedResp = await resp.text();
    expect(parsedResp).toEqual("Parent worker sees: Hello World!");
  });
});
```
{{</tab>}}
{{</tabs>}}

## `getPlatformProxy`.

`getPlatformProxy `函数提供了一种获取对象的方法，该对象包含代理(与**本地**`workerd `绑定)和 Cloudflare Workers 特定值的仿真，允许在 Node.js 进程中进行仿真。

{{<Aside type="warning">}}

根据设计，`getPlatformProxy` 只能在 Node.js 应用程序中使用。`getPlatformProxy` 不能在 Workers 运行时内运行。

{{</Aside>}}

获取平台代理的一个一般用例是在以 Workers 为目标的应用程序中模拟绑定，但在 Workers 运行时之外运行(例如，在 Node.js 中运行的本地开发服务器框架)，或者用于测试目的(例如，确保代码与某种类型的绑定正确交互)。

{{<Aside type="note">}}

该函数提供的绑定代理是对真实生产绑定的尽力模拟。虽然它们的设计尽可能接近真实情况，但两者之间可能存在细微差别和不一致。

{{</Aside>}}

### 语法

```js
const platform = await getPlatformProxy(options);
```

### 参数

{{<definitions>}}

*   `options` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

    * 包含绑定首选项的可选选项对象：

        * `environment` {{<type>}}string{{</type>}}

          使用环境。

        * `configPath` {{<type>}}string{{</type>}}

          要使用的配置文件的路径。

          如果未指定路径，默认行为是在文件系统中从当前目录向上搜索要使用的 `wrangler.toml`。

          **注意：**该字段为可选项，但如果指定了路径，则必须指向文件系统中的有效文件。

        * `experimentalJsonConfig` {{<type>}}boolean{{</type>}}

          `true`，则允许实用程序读取 JSON 配置文件(例如，`wrangler.json`)。

        * `persist` {{<type>}}boolean | { path: string }{{</type>}}

          指示是否持久化绑定数据以及持久化的位置。如果是 `true` 或 `undefined`，默认与 Wrangler 使用的位置相同，因此数据可在它和调用者之间共享。如果是 `false`，则不会将数据持久化到文件系统，也不会从文件系统读取数据。

          **注意：**如果使用 `wrangler` 的 `--persist-to` 选项，请注意该选项会在引擎盖下添加一个名为 `v3` 的子目录，而 `getPlatformProxy` 的 `persist` 不会。例如，如果运行 `wrangler dev --persist-to ./my-directory`，要使用 `getPlatformProxy` 重用同一位置，则必须指定：`persist:"./my-directory/v3"`。

{{</definitions>}}

### 返回类型

`getPlatformProxy()` 返回一个解析为包含以下字段的对象的 `Promise'。

{{<definitions>}}

*   `env` {{<type>}}Record<string, unknown>{{</type>}}

    * 包含可与生产绑定以相同方式使用的绑定代理的对象。这与作为模块格式化工作者第二个参数传递的 `env` 对象的形状相匹配。这些绑定代理实现在 `workerd` 中运行。
    * Typescript 提示：`getPlatformProxy<Env>()` 是一个通用函数。你可以将绑定记录的形状作为类型参数传递，以获得不含`unknown`值的适当类型。

*   `cf` {{<type-link href="/workers/runtime-apis/request/#incomingrequestcfproperties">}}IncomingRequestCfProperties{{</type-link>}} {{<prop-meta>}}read-only{{</prop-meta>}}

    * 模拟 `Request` 的 `cf` 属性，包含与生产中类似的数据。

*   `ctx` {{<type>}}object{{</type>}}

    * 包含 [`waitUntil`](/workers/runtime-apis/context/#waituntil) 和 [`passThroughOnException`](/workers/runtime-apis/context/#passthroughonexception) 函数实现的模拟对象。

*   `caches` {{<type>}}object{{</type>}}

    * 仿真 [Workers `caches` runtime API](/workers/runtime-apis/cache/)。
    * 目前，所有高速缓存操作都不起作用。我们将很快提供更精确的模拟。

*   `dispose()` {{<type>}}() => Promise\<void>{{</type>}}

    * 终止底层的 `workerd` 进程。
    * 在程序不再需要平台代理后调用该函数。如果运行的是可无限期使用代理的长期进程(如开发服务器)，则无需调用此函数。

{{</definitions>}}


### 使用方法

`getPlatformProxy `函数使用 `wrangler.toml `中的绑定。例如，如果你在`wrangler.toml`中设置了[环境变量](/workers/configuration/environment-variables/#add-environment-variables-via-wrangler)配置：

```js
[vars]
MY_VARIABLE = "test"
```

你可以像这样导入 `getPlatformProxy` 来访问绑定：

```js
import { getPlatformProxy } from "wrangler";

const { env } = await getPlatformProxy();
```

要访问 `MY_VARIABLE` 绑定的值，请在代码中添加以下内容：

```js
console.log(`MY_VARIABLE = ${env.MY_VARIABLE}`);
```

这将打印以下输出：`MY_VARIABLE = test`.

### 支持的绑定

你可以通过 `env` 使用 `wrangler.toml` 中的所有支持绑定。

`getPlatformProxy` 支持的绑定包括

* [环境变量](/workers/configuration/environment-variables/)

* [服务绑定](/workers/runtime-apis/bindings/service-bindings/)

* [KV 名称空间绑定](/kv/api/)

* [耐用对象绑定](/durable-objects/api/)

     * 要使用与 `getPlatformProxy` 的持久对象绑定，请务必 [指定一个 `script_name`](/workers/wrangler/configuration/#durable-objects) 并通过 [`wrangler dev`](/workers/wrangler/commands/#dev) 在单独的终端中运行目标 Worker。

        例如，`getPlatformProxy`可能会读取以下文件。

        ```toml
        ---
        filename: wrangler.toml
        ---
        [[durable_objects.bindings]]
        name = "MyDurableObject"
        class_name = "MyDurableObject"
        script_name = "my-worker"
        ```

        为了让`getPlatformProxy`成功代理此绑定，需要一个名为`my-worker`的 Worker
        必须运行使用相同`class_name`的`MyDurableObject`持久对象声明的
        分别通过 `wrangler dev`.


* [R2 桶绑定](/r2/api/workers/workers-api-reference/)

* [队列绑定](/queues/configuration/javascript-apis/)

* [D1 数据库绑定](/d1/build-with-d1/d1-client-api/)

* [Works人工智能绑定](/workers-ai/get-started/workers-wrangler/#2-connect-your-worker-to-workers-ai)

    {{<render file="_ai-local-usage-charges.md" productFolder="workers">}}
