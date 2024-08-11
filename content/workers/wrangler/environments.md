---
pcx_content_type: concept
title: 环境
meta:
  description: 为每个环境部署具有不同配置的相同 Worker 应用程序。
---

# 环境

## 背景

Wrangler 允许你在部署同一个 Worker 应用程序时，为每个环境配置不同的配置。你必须在 Worker 应用程序的 `wrangler.toml` 文件中配置环境。

查看以下环境流程：

1. 你创建了一个名为 `my-worker `的 Worker 应用程序。
2. 在 Worker 的 `wrangler.toml` 配置文件中创建一个环境，例如`dev`。
3. 在`wrangler.toml`中，你可以通过[添加绑定](/workers/runtime-apis/bindings/)和/或[路由](/workers/configuration/routing/routes/)来配置`dev`环境。
4. 使用 `npx wrangler deploy -e dev` 部署 Worker。
5. 在后台，Wrangler 创建了一个名为 `my-worker-dev `的新 Worker。
6. 现在，你可以更改 `my-worker `Worker 代码和配置，并选择将更改部署到哪个环境。

在 `wrangler dev`、`npx wrangler deploy` 和 `wrangler secret` 中使用`--env`或`-e`标记时，就可以使用环境。

## 配置

创造环境：

1. 打开 Worker 的`wrangler.toml`文件。
2. 添加 `[env.<NAME>]`，并将 `<NAME>` 改为所需的环境名称。
3. 重复步骤 2，创建多个环境。

在命名环境时，请注意不要包含敏感信息，如 `migrating-service-from-company1-to-company2 `或 `company1-acquisition-load-test`。

查看设置了自定义 `dev.example.com `路由的`[env.dev]`环境示例的布局：

```toml
---
filename: wrangler.toml
---
name = "your-worker"
route = "example.com"

[env.dev]
route = "dev.example.com"
```

不能指定多个相同名称的环境。

在部署 Worker 时，Wrangler 会将环境名称附加到顶层名称上。例如，如果 Worker 项目名为 `my-worker`，且环境为 `[env.dev]`，则将部署名为 `my-worker-dev`的 Worker。

配置环境后，在 Worker 项目目录下运行 `npx wrangler deploy` 使更改生效。

## 不可继承的键和环境

[非继承键](/workers/wrangler/configuration/#non-inheritable-keys)可在顶层配置，但不能被环境继承，必须为每个环境指定。

[绑定](/workers/runtime-apis/bindings/) 和[环境变量](/workers/configuration/environment-variables/) 必须在[`wrangler.toml`](/workers/wrangler/configuration/) 文件中为每个[环境](/workers/wrangler/environments/) 指定。

查看下面的示例 `wrangler.toml` 文件：

```toml
---
filename: wrangler.toml
---
name = "my-worker"

vars = { API_HOST = "example.com" }

kv_namespaces = [
  { binding = "<BINDING_NAME>", id = "<KV_NAMESPACE_ID_DEV>" }
]

[env.production]

vars = { API_HOST = "production.example.com" }

kv_namespaces = [
  { binding = "<BINDING_NAME>", id = "<KV_NAMESPACE_ID_PRODUCTION>" }
]
```

你可以通过运行[`wrangler secret put <KEY> -env`](/workers/wrangler/commands/#put-3)命令指定特定环境的[secrets](/workers/configuration/secrets/)。

---

## Examples

### Staging and production environments

The following `wrangler.toml` file adds two environments, `[env.staging]` and `[env.production]`, to the `wrangler.toml` file. If you are deploying to a [Custom Domain](/workers/configuration/routing/custom-domains/) or [route](/workers/configuration/routing/routes/), you must provide a [`route` or `routes` key](/workers/wrangler/configuration/) for each environment.

```toml
---
filename: wrangler.toml
---
name = "my-worker"
route = "dev.example.com/*"
vars = { ENVIRONMENT = "dev" }

[env.staging]
vars = { ENVIRONMENT = "staging" }
route = "staging.example.com/*"

[env.production]
vars = { ENVIRONMENT = "production" }
routes = [
  "example.com/foo/*",
  "example.com/bar/*"
]
```

为了使用具有此配置的环境，您可以通过`--env`标志传递环境名称。

通过此配置，Wrangler 将按以下方式运行：

```sh
~/my-worker $ npx wrangler deploy
Uploaded my-worker
Published my-worker
  dev.example.com/*
```

```sh
~/my-worker $ npx wrangler deploy --env staging
Uploaded my-worker-staging
Published my-worker-staging
  staging.example.com/*
```

```sh
~/my-worker $ npx wrangler deploy --env production
Uploaded my-worker-production
Published my-worker-production
  example.com/*
```

任何定义的[环境变量](/workers/configuration/environment-variables/)（[`vars`](/workers/wrangler/configuration/) 键）都会作为全局变量公开给您的 Worker。

通过此配置，`ENVIRONMENT`变量可用于根据给定环境调用特定代码：

```js
if (ENVIRONMENT === "staging") {
  // staging-specific code
} else if (ENVIRONMENT === "production") {
  // production-specific code
}
```

### Staging environment with \*.workers.dev

要将代码部署到`*.workers.dev`子域，请在所需环境中包含`workers_dev = true`。您的`wrangler.toml`文件可能如下所示：

```toml
---
filename: wrangler.toml
---
name = "my-worker"
route = "example.com/*"

[env.staging]
workers_dev = true
```

通过此配置，Wrangler 将按以下方式运行：

```sh
~/my-worker $ npx wrangler deploy
Uploaded my-worker
Published my-worker
  example.com/*
```

```sh
~/my-worker $ npx wrangler deploy --env staging
Uploaded my-worker
Published my-worker
  https://my-worker-staging.<YOUR_SUBDOMAIN>.workers.dev
```

{{<Aside type="warning">}}

当您通过环境创建 Worker 时，Cloudflare 会自动为其创建 SSL 认证。 SSL 认证是可发现的并且是公共记录的问题。

{{</Aside>}}
