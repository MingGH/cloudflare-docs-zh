---
pcx_content_type: configuration
title: 回滚
meta:
  description: 恢复到旧版本的 Worker。
---

{{<heading-pill style="beta">}}Rollbacks{{</heading-pill>}}

你可以使用 [Wrangler](/workers/wrangler/commands/#rollback)或 Cloudflare 控制面板回滚到之前部署的 Worker [版本](/workers/configuration/versions-and-deployments/#versions)。回滚到之前版本的 Worker 将立即创建一个新的 [部署](/workers/configuration/versions-and-deployments/#deployments)，并指定版本，成为所有已部署路由和域的活动部署。

## 通过Wrangler

要通过 Wrangler 回滚到指定版本的 Worker，请使用 [`wranglerrollback`](/workers/wrangler/commands/#rollback)命令。

## 通过 Cloudflare 控制面板

通过 Cloudflare 控制面板回滚到指定版本的 Worker：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/?to=/:account/workers) 并选择你的账户。
2. 进入 **Works和页面**> 选择Works > **部署**。
3. 选择要回滚到的版本右侧的三点图标，然后选择 **回滚**。

{{<Aside type="warning">}}

**在回滚过程中，**[连接到你的 Worker 的资源](/workers/runtime-apis/bindings/) 不会被更改。

如果数据结构在活动部署的版本和选择回滚的版本之间发生变化，那么使用先前版本的代码可能会发生错误。

{{</Aside>}}
## Limits

### 回滚限制

你只能回滚到最近发布的 10 个版本。

### 绑定

如果[Cloudflare 开发人员平台资源](/workers/runtime-apis/bindings/)(如 [KV](/kv/) 和 [D1](/d1/))在选择回滚的版本和活动部署的版本之间被删除或修改，则无法回滚到 Worker 的以前版本。具体来说，在以下情况下不允许回滚

- 活动部署中的版本与选择回滚的版本之间发生了 [耐用对象迁移](/durable-objects/reference/durable-objects-migrations/)。
- 如果目标部署有一个 [binding](/workers/runtime-apis/bindings/)，指向不再存在的 R2 桶、KV 命名空间或队列。