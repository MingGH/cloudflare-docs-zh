---
pcx_content_type: reference
title: 定价
weight: 12
---

# 定价

对你的功能的请求作为 Cloudflare Workers 请求计费。Workers 计划和定价请参阅 [Workers 文档](/workers/platform/pricing/)。

## 付费计划

对页面功能的请求将计入工人付费计划的配额，包括从功能到 KV 或耐用对象(Durable Object)绑定的请求。

页面支持 [标准使用模式](/workers/platform/pricing/#example-pricing-standard-usage-model)。

{{<Aside type="note">}}

Workers企业账户根据其合同中指定的使用模式计费。要切换到标准使用模式，请联系你的客户成功经理 (CSM)。一些 Workers 企业客户可以[更改使用模式](/workers/platform/pricing/#how-to-switch-usage-models)。

{{</Aside>}}

### 静态资产请求

在免费和付费计划中，对静态资产的请求都是免费且无限制的。不调用函数的请求被视为静态请求。请参阅 [函数调用路径](/pages/functions/routing/#functions-invocation-routes) 了解更多有关何时调用函数的信息。

## 免费计划

对页面功能的请求将计入 Workers 免费计划的配额。例如，你可以使用 50,000 个 "Functions"请求和 50,000 个 "Workers"请求来使用 100,000 个每日请求的全部用量。免费计划的每日请求限额在午夜（世界协调时）重置。

在 "项目设置 "中，有两种模式可决定功能在达到每日请求限制后的行为方式：1) 失败打开 和 2) 失败关闭。

### 失败打开

一旦达到每日请求限制，处于失败打开模式的项目将绕过该功能并阻止其对传入流量进行操作。传入请求的行为就像没有函数一样，并传递到站点的静态资产。这是所有 Pages 项目的默认配置。

### 失败关闭

一旦达到每日请求限制，处于故障关闭模式的项目将向访问者显示 Cloudflare 1027 错误页面，表明功能已被暂时禁用。如果你的功能正在执行与安全相关的任务，Cloudflare 建议使用此选项。

{{<Aside type="note">}}

所有页面项目的默认配置都是 "失败打开"。每个项目都可以通过导航到 "项目设置 "页面配置 "失败关闭"。

{{</Aside>}}