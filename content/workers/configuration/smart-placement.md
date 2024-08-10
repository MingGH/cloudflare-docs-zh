---
pcx_content_type: concept
title: Smart Placement (beta)
meta:
  description: Speed up your Worker application by automatically placing your workloads in an optimal location that minimizes latency.
---

{{<heading-pill style="beta">}}Smart Placement{{</heading-pill>}}

默认情况下，[Worker](/workers/) 和 [Pages Functions](/pages/functions/) 会在最靠近接收请求的数据中心调用。如果你在 Worker 中运行后端逻辑，那么在更靠近后端基础架构而不是终端用户的地方运行 Worker 可能会更有效。Smart Placement功能可自动将工作负载放置在最佳位置，从而最大限度地减少延迟并加快应用速度。

如果你要多次往返于集中式数据库、API 或 Worker 中的源服务器，你可能会从 Smart Placement 中受益。

## 背景

下面的示例演示了如何将 Worker 移至靠近后端服务的位置，以减少应用延迟：

澳大利亚悉尼的一名用户正在访问一个运行在 Workers 上的应用程序。为了满足用户的请求，该应用程序多次往返于位于德国法兰克福的数据库。

![位于澳大利亚悉尼的用户连接到同一地区的 Worker，然后该 Worker 会多次往返于位于德国法兰克福的数据库。](/images/workers/platform/workers-smart-placement-disabled.png)

问题在于 Worker 多次往返数据库所需的时间。启用Smart Placement后，Cloudflare 网络将在最靠近数据库的数据中心处理请求，而不是在靠近用户的地方处理请求。

![位于澳大利亚悉尼的用户连接到位于德国法兰克福的 Worker，然后 Worker 会多次往返于同样位于德国法兰克福的数据库。](/images/workers/platform/workers-smart-placement-enabled.png)

## 了解Smart Placement的工作原理

Smart Placement功能是按 Worker 启用的。启用后，将定期分析来自 Worker 的获取请求(也称为子请求)。Smart Placement算法会确定最佳放置位置，以尽量减少 Worker 与 Worker 通信的后端服务之间的往返时间 (RTT)。

Smart Placement仅适用于 **往返后端基础设施一次以上** 的 Worker。如果你的 Worker 平均处理的子请求少于一个，智能分区将在离用户最近的数据中心运行 Worker。

Smart Placement是一种尽力而为的尝试。除非Smart Placement比默认值(即在最靠近用户的数据中心运行 Worker)性能更高，否则不会采取行动。

Smart Placement 只影响 [fetch 事件处理程序](/workers/runtime-apis/handlers/fetch/) 的执行。没有 `提取 `事件处理程序的 Worker 将被 Smart Placement 忽略。对于既有提取事件处理程序又有非提取事件处理程序的 Worker，智能布局只会影响提取事件处理程序的执行。

### D1

绑定了 [D1](/d1/) 的 Worker 将始终被放置在其绑定的 D1 数据库位置附近的数据中心。在这种情况下，Smart Placement会忽略对其他后端服务的子请求。

### 不支持的后端服务

有些后端服务不在智能配售算法的考虑范围内：

- **全球分布式服务**：如果与你的 Worker 通信的服务在地理上分布在许多地区(例如，CDN、分布式数据库、分布式 API)，则不适合使用 `Smart Placement`。我们会自动将这些服务排除在Smart Placement优化之外。
    - 例如：Google API、使用 Fastly 或 Akamai CDN 的服务：Google API、使用 Fastly 或 Akamai CDN 的服务。


- **分析或日志服务**：向分析或日志服务发出的请求不应处于应用程序的关键路径中。应使用 [`waitUntil()`](/workers/runtime-apis/context/#waituntil)，以便在检测代码时不会阻止向用户返回响应。由于从用户角度来看，`waitUntil()` 不会影响请求持续时间，因此我们会自动将分析和日志服务排除在Smart Placement优化之外。
    - 例如：New Relic、Datadog、Tinybird、Grafana、Amplitude、Honeycomb。

## 启用Smart Placement

Smart Placement功能适用于所有Works计划的用户。

### 通过 Wrangler 启用Smart Placement

要通过 Wrangler 启用Smart Placement：

1. 确保已[安装](/workers/wrangler/install-and-update/)`wrangler@2.20.0`或更高版本。
2. 在 Worker 项目的`wrangler.toml`文件中添加以下内容：

    ```toml
    [placement]
    mode = "smart"
    ```

3. 向 Worker 发送一些初始流量(约 20-30 个请求)。向 Worker 发送流量后需要几分钟，Smart Placement才会生效。

4. 查看Works的 [申请期限分析](/workers/observability/metrics-and-analytics/)。


### 通过仪表板启用Smart Placement功能

通过仪表板启用Smart Placement：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**Workers & Pages**。
3. 在**概览**中，选择你的Works。
4. 选择 **设置**> **常规**。
5. 在**位置**下，选择**智能**。
6. 向 Worker 发送一些初始流量(约 20-30 个请求)。向 Worker 发送流量后需要几分钟，Smart Placement才会生效。
7. 查看Works的[request duration analytics](/workers/observability/metrics-and-analytics/)。

## 可观察性

### 安置情况

Worker 的元数据包含有关 Worker 安置状态的详细信息。你可以通过以下 Workers API 接口查询 Worker 的安置状态：

```bash
$ curl -X GET https://api.cloudflare.com/client/v4/accounts/{ACCOUNT_ID}/workers/services/{WORKER_NAME} \
-H "Authorization: Bearer <TOKEN>" \
-H "Content-Type: application/json" | jq .
```

可能的placement状态包括
- _(not present)_:：尚未对Works进行Smart Placement分析。
- `INSUFFICIENT_INVOCATIONS`：没有足够的请求让Smart Placement做出安置决定。
- `NO_VALID_HOSTS`：Worker 不会向 Smart Placement 支持的后端服务发送子请求。
- `INSUFFICIENT_SUBREQUESTS`：Worker 没有向有效的后端服务发送足够的子请求。
- `SUCCESS`：已成功分析 Worker，并将通过 Smart Placement 进行优化。

### 请求持续时间分析

启用Smart Placement后，将收集有关请求持续时间的数据。请求持续时间是在离最终用户最近的数据中心测量的。

默认情况下，有百分之一 (1%) 的请求未使用Smart Placement。这些请求可作为比较基准。

### `cf-placement` header

启用Smart Placement后，Cloudflare 会在所有请求中添加一个 `cf-placement` header。这可用于检查请求是否已通过Smart Placement路由，以及 Worker 处理请求的位置(显示为距离数据中心最近的机场代码)。

例如，`cf-placement: remote-LHR` header的`remote`值表示请求通过Smart Placement路由到伦敦附近的 Cloudflare 数据中心。`cf-placement：local-EWR `header的 `local `值表示请求未使用Smart Placement路由，Worker 在最接近接收请求的数据中心(靠近纽瓦克自由国际机场 (EWR))调用。

{{<Aside type="warning" header="Beta use only">}}

我们可能会在 `Smart Placement `全面可用之前删除 `cf-placement `header。

{{</Aside>}}


## 最佳做法

如果你要在 Worker 上构建全栈应用程序，我们建议你将前端和后端逻辑拆分成不同的 Worker，并使用 [Service Bindings](/workers/runtime-apis/bindings/service-bindings/) 来连接前端逻辑和后端逻辑 Worker。

Smart Placement和服务绑定](/images/workers/platform/smart-placement-service-bindings.png)

在后端 Worker 上启用 Smart Placement 将在靠近后端服务的地方调用 Worker，而前端 Worker 则在靠近用户的地方为请求提供服务。这种架构既能保持快速、反应灵敏的前端，又能改善调用后端 Worker 时的延迟。

## 对Smart Placement提出反馈意见

Smart Placement 处于测试阶段。要分享你对Smart Placement的想法和体验，请加入 [Cloudflare 开发人员讨论区](https://discord.cloudflare.com)。