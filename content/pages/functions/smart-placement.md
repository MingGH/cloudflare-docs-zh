---
pcx_content_type: concept
title: 智能放置（测试版）
---

{{<heading-pill style="beta">}}Smart Placement{{</heading-pill>}}

默认情况下，[Workers](/workers/) 和 [Pages Functions](/pages/functions/) 会在最靠近接收请求的数据中心调用。如果你在页面功能中运行后端逻辑，那么在更靠近后端基础架构而非终端用户的地方运行页面功能可能会更高效。智能放置(测试版)可自动将工作负载放置在最佳位置，从而最大限度地减少延迟并加快应用程序的运行速度。

如果你在页面功能中多次往返于集中式数据库、应用程序接口或源服务器，你可能会受益于智能放置功能。

## 背景

智能放置适用于页面功能和中间件。通常情况下，资产总是在全局和最靠近用户的位置提供服务。

页面上的智能放置目前有一些注意事项。虽然资产总是从最靠近用户的位置提供，但这一行为有两个例外：

1. 启用智能放置后，如果每个请求都使用中间件(`functions/_middleware.js`)，所有资产都将从最靠近后端基础架构的位置提供。这可能会导致延迟意外增加。

2. 使用 [`env.ASSETS.fetch`](https://developers.cloudflare.com/pages/functions/advanced-mode/)时，通过页面函数的 `ASSETS` 抓取器提供的资产会从与函数相同的位置提供。这可能是最靠近你的后端基础设施而非用户的位置。


{{<Aside type= "note">}}

要了解智能布局的工作原理，请参阅 [智能布局](/workers/configuration/smart-placement/)。

{{</Aside>}}

## 启用智能放置(测试版)

所有计划均提供智能投放功能。

### 通过仪表板启用智能放置功能

通过仪表板启用智能放置：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在 `账户主页 `中，选择 `**工作者和页面**`。
3. 在**概览**中，选择你的页面项目。
4. 选择 **设置**> **功能**。
5. 在**位置**下，选择**智能**。
6. 向页面功能发送一些初始流量(约 20-30 个请求)。向页面功能发送流量几分钟后，智能布局功能才会生效。
7. 在功能指标下查看页面功能的 [请求持续时间指标](/workers/observability/metrics-and-analytics/)。

## 对智能投放提出反馈意见

Smart Placement 处于测试阶段。要分享你对智能放置的想法和体验，请加入 [Cloudflare 开发人员讨论区](https://discord.cloudflare.com)。