---
pcx_content_type: how-to
title: 配置
weight: 6
---

# 通过 `wrangler.toml` 进行配置

{{<Aside type="warning">}}

如果你的项目包含 [以前用于本地开发](/pages/functions/local-development/) 的现有 `wrangler.toml` 文件 ，请确保在选择使用 `wrangler.toml` 部署你的 Pages 项目之前，验证该文件是否与 Cloudflare 面板中的项目设置相匹配。Cloudflare 建议你使用 `npx wrangler pages download config` 将当前项目设置下载到 `wrangler.toml` 文件中，而不是手动编写 `wrangler.toml` 文件。

{{</Aside>}}

页面功能有两种配置方式，一种是通过 [Cloudflare 仪表板](https://dash.cloudflare.com)，另一种是 `wrangler.toml`，这是一个用于自定义 [Workers](/workers/) 和页面功能的开发和部署设置的配置文件。

本页可作为如何通过 `wrangler.toml` 配置 Pages 项目的参考。

如果使用 `wrangler.toml`，则必须将 `wrangler.toml` 作为 Pages 项目配置的 [source of truth](/pages/functions/wrangler-configuration/#source-of-truth)。

{{<Aside type="note" header="Configuration via `wrangler.toml` is in open beta.">}}

Cloudflare 欢迎你提供反馈。请加入 [Cloudflare 开发人员 Discord](https://discord.com/invite/cloudflaredev) 中的 #functions 频道，报告错误并申请功能。

{{</Aside>}}

使用 `wrangler.toml` 配置你的 Pages 项目，你可以

- **将配置文件保存在源代码控制中：** 将配置与其他代码一起保存在版本库中。
- **通过代码编辑器编辑配置：** 无需在界面之间来回切换。
- **编写跨环境共享的配置：** 在一个文件中定义本地开发、预览和生产环境的配置，如 [绑定](/pages/functions/bindings/)。
- **确保更好的访问控制：** 通过在项目仓库中使用配置文件，你可以控制谁有权进行更改，而无需提供 Cloudflare 控制面板的访问权限。

## `wrangler.toml` 文件示例

```toml
---
filename: wrangler.toml
---
name = "my-pages-app"
pages_build_output_dir = "./dist"

[[kv_namespaces]]
binding = "KV"
id = "<NAMESPACE_ID>"

[[d1_databases]]
binding = "DB"
database_name = "northwind-demo"
database_id = "<DATABASE_ID>"

[vars]
API_KEY = "1234567asdf"
```

## 要求

### V2 构建系统

通过 `wrangler.toml` 进行页面功能配置需要 [V2 构建系统](/pages/configuration/language-support-and-tools/) 或更高版本。要从 V1 升级，请参阅 [V2 构建系统迁移说明](/pages/configuration/language-support-and-tools/#v2-build-system)。

### Wrangler

要在 Pages 项目中使用 `wrangler.toml` 配置，你必须拥有 3.45.0 或更高版本的 Wrangler。要检查 Wrangler 版本、更新 Wrangler 或安装 Wrangler，请参阅 [Install/Update Wrangler](/workers/wrangler/install-and-update/)。

## 从仪表板配置迁移

当前没有 `wrangler.toml` 文件的 Pages 项目的迁移说明与已有 `wrangler.toml` 文件的 Pages 项目的迁移说明不同。请根据你的情况仔细阅读说明，以避免在生产中出现错误。

### 具有现有 `wrangler.toml` 文件的项目

在使用 `wrangler.toml` 定义预览和生产配置之前，可以使用 `wrangler.toml` 定义本地开发中 Pages 项目应使用的 [绑定](/pages/functions/bindings/)。

如果你一直使用 `wrangler.toml` 进行本地开发，你的 Pages 项目中可能已经有了一个类似的文件：


```toml
---
filename: wrangler.toml
---
[[kv_namespaces]]
binding = "KV"
id = "<NAMESPACE_ID>"
```

如果你想在 Pages 项目配置中使用现有的 `wrangler.toml` 文件，则必须这样做：

1. 将 `pages_build_output_dir` 键添加到[构建输出目录](/pages/configuration/build-configuration/#build-commands-and-directories)的适当值(例如，`pages_build_output_dir = "./dist"`.)
2. 在部署之前，仔细查看现有的 `wrangler.toml` 配置，确保其与所需的项目配置一致。

如果将 `pages_build_output_dir` 键添加到 `wrangler.toml` 并部署 Pages 项目，Pages 将使用为本地使用而定义的配置，而这很可能是非生产性的。在确信 `wrangler.toml` 可用于生产之前，请勿部署。

{{<Aside type="warning" header="Overwriting configuration">}}

运行[`wrangler 页面下载配置`](/pages/functions/wrangler-configuration/#projects-without-existing-wranglertoml-file)将用根据 Cloudflare 面板配置生成的`wrangler.toml`文件覆盖现有的`wrangler.toml`文件。只有当你想放弃之前用于本地开发的`wrangler.toml`文件，并重新使用从 Cloudflare 面板提取的配置时，才运行此命令。

{{</Aside>}}

如果不添加 `pages_build_output_dir` 键，就可以继续将 `wrangler.toml` 文件用于本地开发，而无需将其迁移到生产环境中使用。如果不添加 `pages_build_output_dir` 键并运行 `wrangler pages deploy`，就会看到一条警告信息，告诉你缺少字段，该文件将继续仅用于本地开发。

### 没有`wrangler.toml`文件的项目

如果你有一个通过 Cloudflare 面板设置配置的现有 Pages 项目，但项目中没有现有的 `wrangler.toml` 文件，请在 Pages 项目目录中运行 `wrangler pages download config` 命令。`wrangler pages download config` 命令将下载现有的 Cloudflare 面板配置，并在 Pages 项目目录中生成有效的`wrangler.toml`文件。

{{<tabs labels="npm | yarn | pnpm">}}
{{<tab label="npm" default="true">}}

```sh
$ npx wrangler pages download config <PROJECT_NAME>
```

{{</tab>}}
{{<tab label="yarn">}}

```sh
$ yarn wrangler pages download config <PROJECT_NAME>
```

{{</tab>}}
{{<tab label="pnpm">}}

```sh
$ pnpm wrangler pages download config <PROJECT_NAME>
```

{{</tab>}}
{{</tabs>}}

查看生成的 `wrangler.toml` 文件。要开始在 Pages 项目的配置中使用 `wrangler.toml` 文件，请通过 [Git 集成](/pages/get-started/git-integration/) 或 [直接上传](/pages/get-started/direct-upload/) 创建一个新的部署。

### 处理设置为 `最新 `的兼容性日期

在 Cloudflare 仪表板中，你可以将预览部署的兼容性日期设置为 `Latest`。这将确保你的项目始终使用最新的兼容性日期，而无需自己明确设置。

如果使用 `wrangler pages download `命令从配置了 `Latest `的项目中下载 `wrangler.toml`，你的 `wrangler.toml `将具有下载配置文件时可用的最新兼容性日期。Wrangler 不支持像仪表盘那样的 `Latest `功能。使用 `wrangler.toml` 时必须明确设置兼容性日期。

请参阅 [本指南](/workers/configuration/compatibility-dates/) 了解有关兼容日期及其工作方式的更多信息。

## 页面函数和 Worker 使用 `wrangler.toml` 的区别

如果你使用过 [Workers](/workers)，那么你可能已经熟悉 [`wrangler.toml`](/workers/wrangler/configuration/)。在 Pages Functions 项目中使用 [`wrangler.toml`]时，需要注意几个关键的区别：

- 页面功能的`wrangler.toml`文件和 Workers 的对应文件之间的配置字段**不完全匹配**。例如，`main `等特定于 Workers 的配置键不适用于页面函数的 `wrangler.toml`。
- Pages `wrangler.toml` 引入了一个新键 `pages_build_output_dir`，仅用于 Pages 项目。
- 该文件中的 [environments](/pages/functions/wrangler-configuration/#configure-environments) 和配置继承概念与 Workers **不同**。
- 使用该文件后，该文件将成为[source of truth](/pages/functions/wrangler-configuration/#source-of-truth)，这意味着使用该文件后，**无法**编辑仪表板中的相同字段。

## 配置环境

使用 `wrangler.toml` 可以在本地环境、预览部署和生产环境中快速设置配置。

### 本地开发

使用 `wrangler pages dev` 时，`wrangler.toml` 可在本地运行。这意味着你可以快速测试配置更改，而无需登录 Cloudflare 控制面板。请参考以下配置文件示例：

```toml
---
filename: wrangler.toml
---
name = "my-pages-app"
pages_build_output_dir = "./dist"
compatibility_date = "2023-10-12"
compatibility_flags = ["nodejs_compat"]

[[kv_namespaces]]
binding = "KV"
id = "<NAMESPACE_ID>"
```

此 `wrangler.toml` 配置文件会在 Pages 项目中添加 `nodejs_compat` 兼容性标志和 KV 命名空间绑定。使用此 `wrangler.toml` 配置文件在 Pages 项目目录下运行 `wrangler pages dev` 将在本地应用 `nodejs_compat` 兼容性标志，并在 `context.env.KV` 中的 Pages 功能代码中公开 `KV` 绑定。

{{<Aside type="note">}}

有关配置键的完整列表，请参阅 [inheritable keys](#inheritable-keys) 和 [non-inheritable keys](#non-inheritable-keys) 。

{{</Aside>}}

### 生产和预览部署

准备好部署项目后，可通过创建包含 `wrangler.toml` 文件的新部署，为生产和预览部署设置配置。

{{<Aside type="note">}}

对于以下命令，如果你使用的是 git，请务必记住你设置为[生产分支](/pages/configuration/branch-build-controls/#production-branch-control)和[预览分支设置](/pages/configuration/branch-build-controls/#preview-branch-control)的分支。

{{</Aside>}}

要将上述示例用作生产配置，请使用以下命令创建新的生产部署：

```sh
$ npx wrangler pages deploy
```

或者更具体地说

```sh
$ npx wrangler pages deploy --branch <PRODUCTION BRANCH>
```

要为预览部署部署配置，可以在配置为使用 [preview deployments](/pages/configuration/branch-build-controls/#preview-branch-control) 的分支上运行与上述相同的命令。这将为所有预览部署设置配置，而不仅仅是特定分支的部署。Pages 目前不支持基于分支的配置。

{{<Aside type="note">}}

`--branch` 标记在 `wrangler pages deploy `中是可选的。如果使用 git 整合，Wrangler 会根据当前所在的版本库推断出分支，并将其隐式添加到命令中。

{{</Aside>}}

### 特定环境overrides

有时，你可能希望在本地、预览部署和生产部署中使用不同的配置。可以使用 `[env.production]` 或 `[env.preview]`，覆盖生产部署和预览部署的配置。

{{<Aside type="note">}}

与 [Workers Environments](/workers/wrangler/configuration/#environments) 不同，`production `和 `preview `是通过`[env.<ENVIRONMENT>]`提供的唯一两个选项。

{{</Aside>}}

有关如何覆盖预览部署配置的示例，请参阅下面的 `wrangler.toml` 配置文件：

```toml
---
filename: wrangler.toml
---
name = "my-pages-site"
pages_build_output_dir = "./dist"

[[kv_namespaces]]
binding = "KV"
id = "<NAMESPACE_ID>"

[vars]
API_KEY = "1234567asdf"

[[env.preview.kv_namespaces]]
binding = "KV"
id = "<PREVIEW_NAMESPACE_ID>"

[env.preview.vars]
API_KEY = "8901234bfgd"
```

如果通过 `wrangler pages deploy` 部署此文件，`name`、`pages_build_output_dir`、`kv_namespaces` 和 `vars` 将把配置应用于本地和生产，而 `env.preview` 将覆盖预览部署中的 `kv_namespaces` 和 `vars`。

如果你想让配置值应用于本地和预览，但覆盖生产配置，你的文件应该是这样的：

```toml
---
filename: wrangler.toml
---
name = "my-pages-site"
pages_build_output_dir = "./dist"

[[kv_namespaces]]
binding = "KV"
id = "<NAMESPACE_ID>"

[vars]
API_KEY = "1234567asdf"

[[env.production.kv_namespaces]]
binding = "KV"
id = "<PRODUCTION_NAMESPACE_ID>"

[env.production.vars]
API_KEY = "8901234bfgd"
```

你总是可以明确地覆盖预览和制作：

```toml
---
filename: wrangler.toml
---
name = "my-pages-site"
pages_build_output_dir = "./dist"

[[kv_namespaces]]
binding = "KV"
id = "<NAMESPACE_ID>"

[vars]
API_KEY = "1234567asdf"

[[env.preview.kv_namespaces]]
binding = "KV"
id = "<PREVIEW_NAMESPACE_ID>"

[env.preview.vars]
API_KEY = "8901234bfgd"

[[env.production.kv_namespaces]]
binding = "KV"
id = "<PRODUCTION_NAMESPACE_ID>"

[env.production.vars]
API_KEY = "6567875fvgt"
```

## 继承键

可继承键可在顶层进行配置，并可被特定环境配置继承(或覆盖)。

{{<definitions>}}

- `name` {{<type>}}string{{</type>}} {{<prop-meta>}}required{{</prop-meta>}}

  - 页面项目的名称。仅限字母数字和破折号。

- `pages_build_output_dir` {{<type>}}string{{</type>}} {{<prop-meta>}}required{{</prop-meta>}}

  - 项目构建输出文件夹的路径。例如：`./dist`。

- `compatibility_date` {{<type>}}string{{</type>}} {{<prop-meta>}}required{{</prop-meta>}}

  - 格式为 `yyyy-mm-dd` 的日期，用于确定使用哪个版本的 Workers 运行时。请参阅 [兼容日期](/workers/configuration/compatibility-dates/)。

- `compatibility_flags` {{<type>}}string[]{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 启用 Workers 运行时即将推出的功能的标志列表，通常与 `compatibility_date` 一起使用。请参阅 [兼容日期](/workers/configuration/compatibility-dates/)。

- `send_metrics` {{<type>}}boolean{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - Wrangler 是否应为此项目向 Cloudflare 发送使用指标。

- `limits` {{<type-link href="#limits">}}Limits{{</type-link>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 配置运行时对执行施加的限制。请参阅 [限制](#limits)。

- `placement` {{<type>}}Placement{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 指定页面功能的位置，以尽量减少往返时间。请参阅 [Smart Placement](/workers/configuration/smart-placement/)。

- `upload_source_maps` {{<type>}}boolean{{</type>}}

  - 当 `upload_source_maps` 设置为 `true` 时，Wrangler 会上传页面项目中的任何服务器端源代码映射，以便在日志中提供正确的堆栈跟踪。
{{</definitions>}}

## 非继承密钥

非继承键可在顶层进行配置，但如果在任何环境中覆盖了任何一个非继承键(例如，`[[env.production.kv_namespaces]]`)，则必须在环境配置中指定并覆盖所有非继承键。

例如，这种配置将不起作用：

```toml
---
filename: wrangler.toml
---
name = "my-pages-site"
pages_build_output_dir = "./dist"

[[kv_namespaces]]
binding = "KV"
id = "<NAMESPACE_ID>"

[vars]
API_KEY = "1234567asdf"

[env.production.vars]
API_KEY = "8901234bfgd"
```

`[[env.production.vars]]`被设置为覆盖`[vars]`。因此，也必须通过定义 `[[env.production.kv_namespaces]]`来覆盖 `[[kv_namespaces]]`。

这将适用于本地开发，但在尝试部署时将无法验证。

{{<definitions>}}

- `vars` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 部署函数时要设置的环境变量映射。请参阅 [环境变量](/pages/functions/bindings/#environment-variables)。

- `d1_databases` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 你的 Function 应绑定的 D1 数据库列表。请参阅 [D1 数据库](/pages/functions/bindings/#d1-databases)。

- `durable_objects` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 你的函数应绑定的持久对象列表。请参阅 [Durable Objects](/pages/functions/bindings/#durable-objects)。

- `hyperdrive` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 指定你的 Function 应绑定的 Hyperdrive 配置。请参阅 [Hyperdrive](/pages/functions/bindings/#r2-buckets)。

- `kv_namespaces` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 你的 Function 应绑定的 KV 命名空间列表。请参阅 [KV 命名空间](/pages/functions/bindings/#kv-namespaces)。

- `queues.producers` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 指定绑定到此函数的队列生产者。请参阅 [Queues Producers](/queues/get-started/#4-set-up-your-producer-worker)。

- `r2_buckets` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 你的 Function 应绑定的 R2 桶列表。请参阅 [R2 桶](/pages/functions/bindings/#r2-buckets)。

- `vectorize` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 你的 Function 应绑定的 Vectorize 索引列表。请参阅 [Vectorize 索引](/vectorize/get-started/intro/#3-bind-your-worker-to-your-index)。

- `services` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 你的 Function 应与之绑定的服务绑定列表。请参阅 [服务绑定](/pages/functions/bindings/#service-bindings)。

- `analytics_engine_datasets` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 指定绑定到此函数的分析引擎数据集。请参阅 [Workers Analytics Engine](/analytics/analytics-engine/get-started/)。

- `ai` {{<type>}}object{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 指定此功能的人工智能绑定。请参阅 [Workers AI](/pages/functions/bindings/#workers-ai)。
{{</definitions>}}

## Limits

你可以用与 Workers 相同的方式为 Pages 项目配置限制。详情请阅读 [本指南](/workers/wrangler/configuration/#limits)。

## 绑定

[绑定](/pages/functions/bindings/) 使你的页面函数能够与 Cloudflare 开发者平台上的资源交互。使用绑定将你的页面功能与 Cloudflare 资源(如 [KV](/kv/)、[Durable Objects](/durable-objects/)、[R2](/r2/) 和 [D1](/d1/))集成。你可以为生产环境和预览环境设置绑定。

### D1 数据库

[D1](/d1/) 是 Cloudflare 的无服务器 SQL 数据库。通过为 D1 的[客户端 API](/d1/build-with-d1/d1-client-api/)创建与每个数据库的[绑定](/workers/runtime-apis/bindings/)，功能可以查询 D1 数据库(或多个数据库)。

{{<Aside type="note">}}

在默认本地开发模式下使用 Wrangler 时，文件将写入本地存储，而不是预览或生产数据库。详情请参阅 [本地开发](/workers/testing/local-development/)。

{{</Aside>}}

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#d1-databases)配置 D1 数据库绑定，配置方式与 Cloudflare Workers 相同。
- 与[D1 数据库绑定](/pages/functions/bindings/#d1-databases)互动。

### Durable Objects

[耐用对象](/durable-objects/) 为 Workers 平台提供低延迟协调和一致的存储。

- 通过 [`wrangler.toml` 文件](/workers/wrangler/configuration/#durable-objects)配置持久对象命名空间绑定，配置方式与 Cloudflare Workers 相同。

{{<Aside type="warning">}}

{{<render file="_do-note.md" productFolder="pages">}} Durable Object bindings configured in a Pages project's `wrangler.toml` require the `script_name` key. For Workers, the `script_name` key is optional.

{{</Aside>}}

- 与你的[耐用对象命名空间绑定](/pages/functions/bindings/#durable-objects)交互。

### 环境变量

[环境变量](/workers/configuration/environment-variables/) 是一种绑定类型，允许你将文本字符串或 JSON 值附加到页面函数。

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#environment-variables)配置环境变量，配置方式与 Cloudflare Workers 相同。
- 与 [环境变量] 交互(/pages/functions/bindings/#environment-variables)。

### Hyperdrive

通过[Hyperdrive](/hyperdrive/)绑定，你可以在 Pages 函数中与任何 Postgres 数据库进行交互和查询。

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#hyperdrive)配置 Hyperdrive 绑定，配置方式与 Cloudflare Workers 相同。

### KV 命名空间

[Workers KV](/kv/api/)是一个全球性、低延迟、键值数据存储。它在少量集中式数据中心存储数据，然后在访问后将数据缓存到 Cloudflare 的数据中心。

{{<Aside type="note">}}

在默认本地开发模式下使用 Wrangler 时，文件将写入本地存储空间，而不是预览或生产命名空间。详情请参阅 [本地开发](/workers/testing/local-development/)。

{{</Aside>}}

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#kv-namespaces)配置 KV 命名空间绑定，配置方式与 Cloudflare Workers 相同。
- 与你的 [KV 命名空间绑定](/pages/functions/bindings/#kv-namespaces) 进行交互。

### Queues Producers

[队列](/queues/) 是 Cloudflare 的全局消息队列服务，提供[保证交付](/queues/reference/delivery-guarantees/) 和[消息批处理](/queues/configuration/batching-retries/)。[队列生产者](/queues/configuration/javascript-apis/#producer)可让你在页面功能中将消息发送到队列中。

{{<Aside type="note">}}

目前无法使用页面功能配置 [queues consumer](/queues/reference/how-queues-works/#consumers)。

{{</Aside>}}

- 通过 [`wrangler.toml` 文件](/workers/wrangler/configuration/#queues)配置队列生产者绑定，配置方式与 Cloudflare Workers 相同。
- 与你的 [Queues Producer binding](/pages/functions/bindings/#queue-producers)互动。

### R2 存储桶

[Cloudflare R2 Storage](/r2)允许开发人员存储大量非结构化数据，而无需支付与典型云存储服务相关的昂贵出口带宽费用。

{{<Aside type="note">}}

在默认本地开发模式下使用 Wrangler 时，文件将写入本地存储，而不是预览或生产桶。详情请参阅 [本地开发](/workers/testing/local-development/)。

{{</Aside>}}

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#r2-buckets)配置 R2 存储桶绑定，配置方式与 Cloudflare Workers 相同。
- 与 [R2 存储桶绑定] 进行交互(/pages/functions/bindings/#r2-buckets)。

### Vectorize indexes

[矢量化索引](/vectorize/)允许你插入和查询矢量嵌入，用于语义搜索、分类和其他矢量搜索用例。

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#vectorize-indexes)配置 Vectorize 绑定，配置方式与 Cloudflare Workers 相同。

### Service bindings

服务绑定允许你在 Pages 函数中调用 Worker。将 Pages 函数绑定到 Worker 后，你就可以向 Worker 发送 HTTP 请求，而无需通过互联网。请求会立即调用下游 Worker，与请求第三方服务相比减少了延迟。请参阅 [关于服务绑定](/workers/runtime-apis/bindings/service-bindings/)。

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#service-bindings)配置服务绑定，配置方式与 Cloudflare Workers 相同。
- 与[服务绑定](/pages/functions/bindings/#service-bindings)互动。

### 分析引擎数据集

[Workers 分析引擎](/analytics/analytics-engine/) 通过页面函数提供分析、可观察性和数据记录功能。在 Pages 函数绑定中写入数据点，然后使用 [SQL API](/analytics/analytics-engine/sql-api/) 查询数据。

- 通过 [`wrangler.toml`文件](/workers/wrangler/configuration/#analytics-engine-datasets)配置分析引擎数据集绑定，配置方式与 Cloudflare Workers 相同。
- 与 [Analytics Engine Dataset](/pages/functions/bindings/#analytics-engine) 交互。

### Works人工智能

[Workers AI](/workers-ai/)允许你在 Cloudflare 网络上通过自己的代码运行机器学习模型，无论是通过 Workers、Pages 还是通过 REST API。

{{<render file="_ai-local-usage-charges.md" productFolder="workers">}}

与其他绑定不同的是，这种绑定仅限于每个 `Pages Function `项目使用一种 AI 绑定。

- 通过 [`wrangler.toml` 文件](/workers/wrangler/configuration/#workers-ai)配置 Workers AI 绑定，配置方式与 Cloudflare Workers 相同。
- 与你的 [Workers AI 绑定](/pages/functions/bindings/#workers-ai)互动。

## 本地开发设置

你可以配置的本地开发设置对于页面功能和 Cloudflare Workers 都是一样的。详情请阅读 [本指南](/workers/wrangler/configuration/#local-development-settings)。

## Source of truth

在你的 Pages Functions 项目中使用时，你的 `wrangler.toml` 文件是最开始的来源。登录 Cloudflare 控制面板后，你可以看到相同的字段，但无法编辑。

如果你决定不使用 `wrangler.toml` 进行配置，可以安全地删除它并创建新的部署。上次部署中的配置值仍将适用，你可以在仪表板上对其进行编辑。