---
pcx_content_type: concept
title: Sentry
weight: 1
---

# 哨兵页面插件

Sentry Pages 插件会捕获并记录页面函数执行链中发生的所有异常。因此，建议将此插件作为第一个插件安装在应用程序根目录下的 `functions/_middleware.ts` 中。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-sentry
```

## 使用方法

```typescript
---
filename: functions/_middleware.ts
---
import sentryPlugin from "@cloudflare/pages-plugin-sentry";

export const onRequest: PagesFunction = sentryPlugin({
  dsn: "https://sentry.io/welcome/xyz",
});
```

该插件使用 [Toucan](https://github.com/robertcepa/toucan-js)。请参阅 Toucan README [查看它可以使用的选项](https://github.com/robertcepa/toucan-js#other-options)。`context`、`request `和 `event `是自动填充的，无需手动配置。

如果 [DSN](https://docs.sentry.io/product/sentry-basics/dsn-explainer/)是作为环境变量或在 KV 中保存的，则可以这样访问它：

```typescript
---
filename: functions/_middleware.ts
---
import sentryPlugin from "@cloudflare/pages-plugin-sentry";

export const onRequest: PagesFunction<{
  SENTRY_DSN: string;
}> = (context) => {
  return sentryPlugin({ dsn: context.env.SENTRY_DSN })(context);
};
```

```typescript
---
filename: functions/_middleware.ts
---
import sentryPlugin from "@cloudflare/pages-plugin-sentry";

export const onRequest: PagesFunction<{
  KV: KVNamespace;
}> = async (context) => {
  return sentryPlugin({ dsn: await context.env.KV.get("SENTRY_DSN") })(context);
};
```

### 补充背景

如果需要为 Sentry 设置其他上下文(例如用户信息或其他日志)，请在执行链中插件下方的任何函数中使用 `data.sentry` 实例。

例如，你可以访问 `data.sentry` 并设置用户信息：

```typescript
---
filename: functions/admin/_middleware.ts
---
import type { PluginData } from "@cloudflare/pages-plugin-sentry";

export const onRequest: PagesFunction<unknown, any, PluginData> = async ({
  data,
  next,
}) => {
  // Authenticate the user from the request and extract user's email address
  const email = await getEmailFromRequest(request);

  data.sentry.setUser({ email });

  return next();
};
```

同样，完整的功能列表可参见 [Toucan 文档](https://github.com/robertcepa/toucan-js#features)。