---
pcx_content_type: concept
title: Honeycomb
weight: 1
---

# Honeycomb 页面插件

Honeycomb 页面插件会自动向 Honeycomb 发送跟踪信息，以便进行分析和观察。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-honeycomb
```

## 使用方法

下面的使用示例使用了你需要在 Pages 项目设置中设置的环境变量。

```typescript
---
filename: functions/_middleware.ts
---
import honeycombPlugin from "@cloudflare/pages-plugin-honeycomb";

export const onRequest: PagesFunction<{
  HONEYCOMB_API_KEY: string
  HONEYCOMB_DATASET: string
}> = (context) => {
  return honeycombPlugin({
    apiKey: context.env.HONEYCOMB_API_KEY,
    dataset: context.env.HONEYCOMB_DATASET,
  })(context);
}
```

或者，你也可以通过以下方式进行硬编码设置(不建议使用 API 密钥)：

```typescript
---
filename: functions/_middleware.ts
---
import honeycombPlugin from "@cloudflare/pages-plugin-honeycomb";

export const onRequest = honeycombPlugin({
  apiKey: "YOUR_HONEYCOMB_API_KEY",
  dataset: "YOUR_HONEYCOMB_DATASET_NAME",
});
```

该插件基于 `@cloudflare/workers-honeycomb-logger`，接受相同的 [配置选项](https://github.com/cloudflare/workers-honeycomb-logger#config)。

确保在 Honeycomb 数据集设置中启用**自动解压缩嵌套 JSON**选项，并将**最大解压缩深度**设为**5**。

![按照上述说明在 Honeycomb 面板中切换 `自动解压缩嵌套 JSON `并将 `最大解压缩深度 `选项设置为 5](/images/pages/platform/functions/honeycomb.png)

### 补充背景

data.honeycomb.tracer `有两种方法可附加有关给定跟踪的附加信息：

- `data.honeycomb.tracer.log `只接受一个参数，即一个 `字符串`。
- `data.honeycomb.tracer.addData `只接受一个参数，即任意数据对象。

有关这些方法的更多信息，请参阅 [`@cloudflare/workers-honeycomb-logger`的文档](https://github.com/cloudflare/workers-honeycomb-logger#adding-logs-and-other-data)。

例如，如果你想使用 `addData` 方法附加用户信息：

```typescript
---
filename: functions/admin/_middleware.ts
---
import type { PluginData } from "@cloudflare/pages-plugin-honeycomb";

export const onRequest: PagesFunction<unknown, any, PluginData> = async ({
  data,
  next,
  request
}) => {
  // Authenticate the user from the request and extract user's email address
  const email = await getEmailFromRequest(request);

  data.honeycomb.tracer.addData({ email });

  return next();
};
```