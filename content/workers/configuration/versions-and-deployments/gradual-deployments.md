---
pcx_content_type: configuration
title: 渐进式部署
meta:
  description: 通过渐进式部署，将代码更改增量部署到您的 Workers。
---

{{<heading-pill style="beta">}}渐进式部署{{</heading-pill>}}

渐进式部署通过在不同版本之间拆分流量，让你能够渐进式地部署 Workers 的新 [版本](/workers/configuration/versions-and-deployments/#versions)。

![渐进部署](/images/workers/platform/version-and-deployments/gradual-deployments.png)

通过渐进式部署，你可以

- 逐步将流量转移到更新版本的 Worker 上。
- 使用 [analytics and logging](/workers/configuration/versions-and-deployments/gradual-deployments/#observability)工具监控各版本的错误率和异常情况。
- 如果在部署新版本时发现问题，可[回滚](/workers/configuration/versions-and-deployments/rollbacks/) 到先前的稳定版本。

{{<Aside type="warning">}}

渐进式部署处于**beta 阶段，正在积极开发中**。使用此功能前，请查看与回滚相关的 [限制](/workers/configuration/version-and-deployments//gradual-deployments/#limits)。

通过 [反馈表](https://www.cloudflare.com/lp/developer-week-deployments)，提供你对回滚功能的反馈意见。

{{</Aside>}}

## 使用渐进式部署

以下部分将指导你使用渐进部署的示例。你将选择使用 [Wrangler](/workers/configuration/version-and-deployments/gradual-deployments/#via-wrangler)或 [Cloudflare dashboard](/workers/configuration/version-and-deployments/gradual-deployments/#via-the-cloudflare-dashboard)：

- 创建新Works。
- 发布该 Worker 的新版本，而不进行部署。
- 在两个版本之间建立渐进式部署。
- 将新版本的部署进度提高到 100% 流量。

### 通过 Wrangler

{{<Aside type="note">}}

所需的最低 Wrangler 程序版本：3.40.0。

{{</Aside>}}

#### 1. 创建并部署新的 Worker

使用 [`create-cloudflare` CLI (C3)](/pages/get-started/c3/) 创建一个新的 `Hello World` Worker 并进行部署。


```sh
$ npm create cloudflare@latest <NAME> -- --type=hello-world
```

回答 `yes `或 `no `使用 TypeScript。请回答 `yes `部署应用程序。这是 Worker 的第一个版本。

#### 2. 创建新版本的Works

要创建新版本的 Worker，请编辑 Worker 代码，将 `Response `内容改为所需文本，然后使用 [`wrangler versions upload`](/workers/wrangler/commands/#upload)命令上传 Worker。

```sh
$ npx wrangler versions upload --experimental-versions
```

这将创建一个不会自动部署的新版本 Worker。

#### 3.创建新的部署

使用[`wrangler versions upload`](/workers/wrangler/commands/#deploy-2)命令来
创建一个新部署，在两个版本的 Worker 之间分配流量。按照交互式提示，使用 [步骤 #1](/workers/configuration/versions-and-deployments/gradual-deployments/#1-create-and-deploy-a-new-worker)和 [步骤 #2](/workers/configuration/versions-and-deployments/gradual-deployments/#2-create-a-new-version-of-the-worker)中上传的版本创建部署。为每个版本选择所需的百分比。

```sh
$ npx wrangler versions deploy --experimental-versions
```

#### 4.测试分拆部署

在 Worker 上运行 cURL 命令来测试拆分部署。

```bash
for j in {0..10}
do
    curl -s https://$WORKER_NAME.$SUBDOMAIN.workers.dev
done
```
你应该会看到 10 个回复。回复将反映部署中各版本返回的内容。响应将根据 [步骤 #3](/workers/configuration/versions-and-deployments/gradual-deployments/#3-create-a-new-deployment)中配置的百分比而有所不同。

你还可以使用 [版本覆盖](#version-overrides)，针对特定版本进行测试。

#### 5.将新版本设置为 100% 部署

再次运行 `wrangler versions deploy` 并按照交互式提示操作。选择 [步骤 2] 中上传的版本(/workers/configuration/versions-and-deployments/gradual-deployments/#2-create-a-new-version-of-the-worker)，并将其设置为 100% 部署。

```sh
$ npx wrangler versions deploy --experimental-versions
```

#### 通过 Cloudflare 仪表板

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/?to=/:account/workers) 并选择你的账户。
2. 转到**Workers & Pages**。
3. 选择 **Create application**> **Hello World**模板 > 部署 Worker。
4. 部署好 Worker 后，通过**编辑代码**进入在线代码编辑器。编辑 Worker 代码(更改 `Response `内容)并上传 Worker。
5. 要保存更改，请选择**部署**> **保存**旁边的**向下箭头**。这将创建一个新版本的 Worker。
6. 进入 **部署**，选择 **部署版本**，创建一个新的部署，在步骤 3 和步骤 5 中创建的两个版本之间分割流量。
7. cURL 你的 Worker 以测试拆分部署。

```bash
for j in {0..10}
do
    curl -s https://$WORKER_NAME.$SUBDOMAIN.workers.dev
done
```
你应该会看到 10 个responses。responses将反映部署中各版本返回的内容。响应将根据步骤 #6 中配置的百分比而有所不同。

## 版本亲和性

默认情况下，使用渐进部署时配置的百分比是按请求进行操作的，即一个请求调用 [部署] 中两个 Worker 版本之一的概率为 X%(/workers/configuration/versions-and-deployments/#deployments)。

你可能希望与特定标识符(如用户、会话或任何唯一标识符)相关联的请求由统一版本的 Worker 处理，以防止版本偏移。如果部署的应用程序有多个版本，而这些版本之间不能正向/反向兼容，就会出现版本偏移。你可以配置版本亲和性，以防止 Worker 的版本按请求来回变化。

你可以通过在向 Worker 发出的传入请求中设置 `Cloudflare-Workers-Version-Key `header来做到这一点。例如

```sh
$ curl -s https://$SCRIPT_NAME.$SUBDOMAIN.workers.dev -H 'Cloudflare-Workers-Version-Key: foo'
```

对于给定的 [部署](/workers/configuration/versions-and-deployments/#deployments)，版本密钥设置为 `foo` 的所有请求都将由相同版本的 Worker 处理。版本密钥 `foo` 所对应的 Worker 具体版本由你在部署中为每个 Worker 版本配置的百分比决定。

你既可以在从互联网向你的 Worker 发出外部请求时设置 `Cloudflare-Workers-Version-Key `header，也可以在使用 [service binding](/workers/runtime-apis/bindings/service-bindings/)从一个 Worker 向另一个 Worker 发出子请求时设置 `Cloudflare-Workers-Version-Key `header。

### 使用规则集引擎设置 `Cloudflare-Workers-Version-Key

你可能希望从请求的某些属性(如 URL、标题或 cookie)中提取版本密钥。你可以在区域上配置[Ruleset Engine](/ruleset-engine/)规则来实现这一目的。这样，你就可以根据这些属性指定版本亲和性，而无需修改发出请求的外部客户端。

例如，如果你的 Worker 在 URI 路径`/assets/`下提供视频资产，而你希望通过一致的版本处理对每个唯一资产的请求，那么你可以定义以下 [request header modification](/rules/transform/request-header-modification/) 规则：

{{<example>}}

**Expression Editor** 中的文本：

```txt
starts_with(http.request.uri.path, "/asset/")
```

修改请求**header**下的选定操作：_Set dynamic_

**Header name**: `Cloudflare-Workers-Version-Key`

**Value**: `regex_replace(http.request.uri.path, "/asset/(.*)", "${1}")`

{{</example>}}

## 版本覆盖

你可以使用版本重载向渐进部署中特定版本的 Worker 发送请求。

要在请求中指定版本覆盖，可在向 Worker 发出的请求中设置 `Cloudflare-Workers-Version-Overrides `头。例如

```sh
$ curl -s https://$SCRIPT_NAME.$SUBDOMAIN.workers.dev -H 'Cloudflare-Workers-Version-Overrides: my-worker-name="dc8dcd28-271b-4367-9840-6c244f84cb40"'
```

`Cloudflare-Workers-Version-Overrides `是一个[字典结构header](https://www.rfc-editor.org/rfc/rfc8941#name-dictionaries)。

字典可以包含多个键值对。每个键表示覆盖应适用的 Worker 名称。值表示应使用的版本 ID，必须是 [String](https://www.rfc-editor.org/rfc/rfc8941#name-strings)。

只有在当前部署中存在指定版本时，才会应用版本覆盖。当前部署中的版本可使用 [`wrangler部署列表 --experimental-versions`](/workers/wrangler/commands/#list---experimental-versions) 命令或在 Worker > Deployments > Active Deployment 下的 [Workers Dashboard](https://dash.cloudflare.com/?to=/:account/workers) 上找到。

{{<Aside type="note" header="Verifying that the version override was applied">}}

有多种原因导致请求的版本覆盖可能无法应用。例如
* 包含指定版本的部署可能尚未传播。
* header值可能不是有效的 [字典](https://www.rfc-editor.org/rfc/rfc8941#name-dictionaries)。

如果未应用请求的版本覆盖，则将根据渐进部署配置中设置的百分比对请求进行路由。

为确保正确应用了请求的版本覆盖，你可以 [观察](#observability) 被调用 Worker 的版本。你甚至可以使用 [runtime binding](#runtime-binding) 在 Worker 的响应中返回版本，从而自动进行检查。

{{</Aside>}}

### 示例

你可能希望先在生产中测试新版本，然后再逐步将其部署到比例越来越高的外部流量中。

在此示例中，部署的初始配置是将所有流量路由到单一版本：

| Version ID                             | Percentage |
| :------------------------------------: | :--------: |
| db7cd8d3-4425-4fe7-8c81-01bf963b6067   | 100%       |

使用 [`wrangler versions deploy --experimental-versions`](/workers/wrangler/commands/#deploy-2) 创建一个新的部署，并为新版本指定 0%，同时将以前的版本保持为 100% 。

| Version ID                             | Percentage |
| :------------------------------------: | :--------: |
| dc8dcd28-271b-4367-9840-6c244f84cb40   | 0%         |
| db7cd8d3-4425-4fe7-8c81-01bf963b6067   | 100%       |

现在，先用版本覆盖测试新版本，然后再将新版本逐步升级到 100%：

```sh
$ curl -s https://$SCRIPT_NAME.$SUBDOMAIN.workers.dev -H 'Cloudflare-Workers-Version-Overrides: my-worker-name="dc8dcd28-271b-4367-9840-6c244f84cb40"'
```

## 渐进式部署耐用对象

由于 [全局唯一性](/durable-objects/platform/known-issues/#global-uniqueness)，每个 [Durable Object](/durable-objects/) 一次只能运行一个版本。这意味着渐进部署对 Durable Objects 的作用略有不同。

当你为 Durable Object Worker 创建新的渐进部署时，每个 Durable Object 实例都会根据你在 [部署](/workers/configuration/versions-and-deployments/#deployments)中配置的百分比分配一个 Worker 版本。在创建新部署之前，该版本不会改变。

![渐进部署耐用对象](/images/workers/platform/version-and-deployments/durable-objects.png)

### 示例

本例假定你先前已创建了 3 个耐用对象，并[从名称导出了它们的 ID](/durable-objects/best-practices/access-durable-objects-from-a-worker/#derive-ids-from-names) `foo`、`bar `和 `baz`。

你的 Worker 目前使用的版本我们称之为 `A `版，而你希望渐进式部署新的 Worker `B `版。

以下是耐用对象版本在渐进式部署过程中可能发生的变化：

| Deployment config                      | "foo" | "bar" | "baz" |
| :------------------------------------: | :---: | :---: | :---: |
| Version A: 100% <br>                   | A     | A     | A     |
| Version B: 20% <br> Version A: 80%     | B     | A     | A     |
| Version B: 50% <br> Version A: 50%     | B     | B     | A     |
| Version B: 100% <br>                   | B     | B     | B     |

这只是一个示例，因此分配给你的耐用对象的版本可能会有所不同。不过，以下内容是可以保证的：

- 对于给定的部署，对每个持久对象的请求将始终使用相同的 Worker 版本。
- 当你以与之前部署相同的顺序指定每个版本，并增加某个版本的百分比时，之前分配给该版本的耐用对象将不会被分配到不同的版本。在此示例中，耐用对象 `foo `永远不会从版本 `B `恢复到版本 `A`。
- 只有当持久对象被分配到不同的版本时，它才会被 [重置](/durable-objects/observability/troubleshooting/#durable-object-reset-because-its-code-was-updated)，因此在本例中，每个持久对象只会被重置一次。

{{<Aside type="note">}}

通常情况下，你的 Durable Object Worker 将同时定义你的 Durable Object 类和与其交互的 Worker。在这种情况下，你不能单独部署对 Durable Object 及其 Worker 的更改。

无论你是否使用渐进部署，都应确保你的 Durable Object 及其 Worker 之间的 API 变动是 [向前和向后兼容的](/durable-objects/platform/known-issues/#code-updates)。不过，使用渐进部署会使不同版本的 Durable Objects 及其 Worker 更有可能相互影响。

{{</Aside>}}

## 可观察性

在使用渐进式部署时，你可能希望将Works调用归因于特定版本，以便了解部署新版本的影响。

### Logpush

Workers Logpush](/workers/observability/logging/logpush/) 中新增了一个 `ScriptVersion` 对象。现在只能通过 [Logpush API](/api/operations/post-accounts-account_identifier-logpush-jobs) 添加 `ScriptVersion` 对象。示例 API 调用：

```bash
curl -X POST 'https://api.cloudflare.com/client/v4/accounts/<ACCOUNT_ID>/logpush/jobs' \
-H 'Authorization: Bearer <TOKEN>' \
-H 'Content-Type: application/json' \
-d '{
"name": "workers-logpush",
"logpull_options": "fields=Event,EventTimestampMs,Outcome,Exceptions,Logs,ScriptName,ScriptVersion",
"destination_conf": "<DESTINATION_URL>",
"dataset": "workers_trace_events",
"enabled": true
}'| jq .
```

`ScriptVersion` 是一个具有以下结构的对象：

```json
scriptVersion: {
    id: "<UUID>",
    message: "<MESSAGE>",
    tag: "<TAG>"
}
```

### 运行时绑定

使用[版本元数据绑定](/workers/runtime-apis/bindings/version-metadata/) 访问 Worker 中的版本 ID 或版本标记。

## Limits

### 部署限制

你只能使用最近 10 个上传的 Worker 版本创建新部署。

### 不支持的功能

这些Works功能将在不久的将来得到支持。

- 不支持通过拆分部署更新 [Secrets via wrangler](/workers/wrangler/commands/#secret)。在使用更新密钥之前，您必须完全部署最新版本。
- 具有 [mTLS 绑定](/workers/runtime-apis/bindings/mtls/) 的 Workers 不支持逐步部署。对具有 mTLS 绑定的 Workers 使用 [`wrangler deploy`](/workers/wrangler/commands/#deploy)。
- 不支持使用 [Durable Objects migrations](/durable-objects/reference/durable-objects-migrations/)创建渐进式部署。如果要应用 [Durable Object migration](/durable-objects/reference/durable-objects-migrations/)，请使用 [`wrangler deploy`](/workers/wrangler/commands/#deploy)。