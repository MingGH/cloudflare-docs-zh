---
pcx_content_type: concept
title: Stytch
weight: 1
---

# Stytch 页面插件

Stytch Pages 插件是一个中间件，用于验证所有请求及其 `session_token`。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-stytch
```

## 使用方法

```typescript
---
filename: functions/_middleware.ts
---
import stytchPlugin from "@cloudflare/pages-plugin-stytch";
import { envs } from "@cloudflare/pages-plugin-stytch/api";

export const onRequest: PagesFunction = stytchPlugin({
  project_id: "YOUR_STYTCH_PROJECT_ID",
  secret: "YOUR_STYTCH_PROJECT_SECRET",
  env: envs.live
});
```

我们建议将秘密存储在 KV 中，而不是上述的纯文本中。

Stytch 插件只有一个参数，即一个包含多个属性的对象。`project_id `和 `secret `是必填字符串，可在［Stytch 的仪表板］(https://stytch.com/dashboard/api-keys) 中找到。`env `也是必填字符串，可通过 API 中的 `envs.test `或 `envs.live `变量填充。默认情况下，插件会验证传入请求中的 `session_token` cookie，但如果使用其他机制来识别用户会话，也可以自行选择传入 `session_token` 或 `session_jwt` 字符串。最后，你还可以传入一个 `session_duration_minutes` 来延长会话的生命周期。有关这些参数的更多信息，请参阅 [Stytch 文档](https://stytch.com/docs/api/session-auth)。

包含用户信息的经过验证的会话响应将提供给`data.stytch.session`上的后续页面函数。