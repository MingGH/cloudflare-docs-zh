---
pcx_content_type: reference
title: Metrics
weight: 11
---

# 衡量标准

函数指标可通过显示函数的性能和使用数据，帮助你诊断问题和了解工作负载。

## Functions衡量标准

功能指标可汇总单个页面项目的请求数据。查看功能指标：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在**账户主页**，选择**工作员和页面**> 在**概览**，选择你的页面项目。
3. 在页面项目中，选择 **功能指标**。

有三个指标可以帮助你了解功能的健康状况：

1. Requests success.
2. Requests errors.
3. Invocation Statuses.

### 请求

在**功能指标**中，你可以看到按总请求数、成功请求数和错误请求数分列的历史请求数。子请求信息可通过选择 **子请求**查看。

* **总数**：功能注册的所有传入请求。被 [Web 应用程序防火墙 (WAF)](https://www.cloudflare.com/waf/) 或其他安全功能阻止的请求将不计算在内。
* **成功**：返回 `Success `或 `Client Disconnected`[调用状态](#invocation-statuses)的请求。
* **错误**：返回 `Script Threw Exception`、`Exceeded Resources `或 `Internal Error `的请求[调用状态](#invocation-statuses)
* **Subrequests**：通过在函数中调用 `fetch` 而触发的请求。当你的函数获取静态资产时，它将算作一个子请求。抛出未捕获错误的子请求将不计算在内。

对于少于六小时的时间范围，请求流量数据可能会在图表中显示的最后几分钟附近出现下降。这并不反映流量的下降，而是汇总和指标交付的轻微延迟。

### 调用状态

函数调用状态表示函数是否成功执行或未能在 Workers 运行时生成响应。调用状态不同于 HTTP 状态代码。在某些情况下，函数调用成功，但由于在 Workers 运行时之外遇到其他错误，因此不会生成成功的 HTTP 状态。某些调用状态会导致向客户端返回 Workers 错误代码。

| Invocation status | Definition | Workers error code | Graph QL field |
| --- | --- | --- | --- |
| Success | Worker script executed successfully | | success |
| Client disconnected | HTTP client disconnected before the request completed | | clientDisconnected |
| Script threw exception | Worker script threw an unhandled JavaScript exception | 1101 | scriptThrewException |
| Exceeded resources^1 | Worker script exceeded runtime limits | 1102, 1027 | exceededResources |
| Internal error^2 | Workers runtime encountered an error | | internalError |

1. 当 Worker 超过[运行时限制](/workers/platform/limits/#request-limits)时，可能会出现 `超过资源 `状态。最常见的原因是 CPU 时间过长，但也可能是脚本超过启动时间或空闲层限制造成的。
2. 由于系统内部故障导致 Workers 运行时无法处理请求时，可能会出现 `内部错误 `状态。这些错误不是由函数代码或资源限制问题引起的。虽然带有内部错误状态的请求很少见，但在正常运行过程中可能会出现一些。这些请求不会计入计费使用量。如果你发现带有 `内部错误 `状态的请求率升高，请查看 www.cloudflarestatus.com。

要进一步研究异常情况，请参阅 [调试和日志记录](/pages/functions/debugging-and-logging)

### 每次执行的 CPU 时间

每次执行的 CPU 时间图表使用[蓄水池取样](https://en.wikipedia.org/wiki/Reservoir_sampling)将历史 CPU 时间数据细分为相关的量化值。  了解有关 [解释量化值] 的更多信息(https://www.statisticshowto.com/quantile-definition-find-easy-steps/)。

在某些情况下，较高的量化值可能会超过 [CPU 时间限制](/workers/platform/limits/#cpu-time)，但不会产生调用错误，这是因为 Workers 运行时中有一种机制允许低于 CPU 限制的请求滚动 CPU 时间。

### 每次执行时间

函数指标**控制面板中**中位 CPU 时间**下方的**持续时间**图表显示了每次函数执行的历史[持续时间](/workers/platform/limits/#duration)。与 CPU 时间图表类似，该数据被细分为相关的量化值。

当你打算在函数本身进行大量计算时，了解函数的持续时间非常有用。这是因为你可能必须使用标准或无约束使用模式，该模式最多允许使用 30 秒的 CPU 时间。

如果[捆绑使用模式](/workers/platform/pricing/#workers)上的 Worker 正在运行许多网络绑定操作(如获取请求和等待响应)，那么即使有 50 毫秒的 CPU 时间限制，它们的持续时间也可能较长。

### 指标保留

Functions指标最多可检查过去三个月的情况，增量最大为一周。页面项目中的**Functions指标**仪表板包括上述图表和信息。