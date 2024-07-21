---
pcx_content_type: concept
title: Cloudflare Access
weight: 1
---

# Cloudflare 访问页面插件

Cloudflare Access Pages Plugin 是验证 Cloudflare Access JWT 断言的中间件。它还包含一个 API，用于查询有关给定用户 JWT 的其他信息。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-cloudflare-access
```

## 使用方法

```typescript
---
filename: functions/_middleware.ts
---
import cloudflareAccessPlugin from "@cloudflare/pages-plugin-cloudflare-access";

export const onRequest: PagesFunction = cloudflareAccessPlugin({
  domain: "https://test.cloudflareaccess.com",
  aud: "4714c1358e65fe4b408ad6d432a5f878f08194bdb4752441fd56faefa9b2b6f2",
});
```

该插件接收一个包含两个属性的对象：Cloudflare Access 帐户的 `domain`(域)和要验证的策略 `aud`(受众)。任何未通过验证的请求都将返回一个 `403` 状态代码。

### 访问 JWT payload

如果你需要在应用程序中使用 JWT 有效载荷(例如，你需要用户的电子邮件地址)，本插件将在 `data.cloudflareAccess.JWT.payload` 中为你提供。

例如

```typescript
---
filename: functions/greet.ts
---
import type { PluginData } from "@cloudflare/pages-plugin-cloudflare-access";

export const onRequest: PagesFunction<unknown, any, PluginData> = async ({
  data,
}) => {
  return new Response(
    `Hello, ${data.cloudflareAccess.JWT.payload.email || "service user"}!`
  );
};
```

[整个 JWT 有效载荷](/cloudflare-one/identity/authorization-cookie/application-token/#payload)将在 `data.cloudflareAccess.JWT.payload` 中提供。请注意，身份授权(例如浏览器中的用户)和非身份授权(例如服务令牌)的可用字段有所不同。

### 查询身份

要获取有关给定用户身份的更多信息，请使用所提供的 `getIdentity` API 函数：

```typescript
---
filename: functions/greet.ts
---
import { getIdentity } from "@cloudflare/pages-plugin-cloudflare-access/api";

export const onRequest: PagesFunction = async ({ data }) => {
  const identity = await getIdentity({
    jwt: "eyJhbGciOiJIUzI1NiIsImtpZCI6IjkzMzhhYmUxYmFmMmZlNDkyZjY0NmE3MzZmMjVhZmJmN2IwMjVlMzVjNjI3YmU0ZjYwYzQxNGQ0YzczMDY5YjgiLCJ0eXAiOiJKV1QifQ.eyJhdWQiOlsiOTdlMmFhZTEyMDEyMWY5MDJkZjhiYzk5ZmMzNDU5MTNhYjE4NmQxNzRmMzA3OWVhNzI5MjM2NzY2YjJlN2M0YSJdLCJlbWFpbCI6ImFkbWluQGV4YW1wbGUuY29tIiwiZXhwIjoxNTE5NDE4MjE0LCJpYXQiOjE1MTkzMzE4MTUsImlzcyI6Imh0dHBzOi8vdGVzdC5jbG91ZGZsYXJlYWNjZXNzLmNvbSIsIm5vbmNlIjoiMWQ4MDgzZjcwOGE0Nzk4MjI5NmYyZDk4OTZkNzBmMjA3YTI3OTM4ZjAyNjU0MGMzOTJiOTAzZTVmZGY0ZDZlOSIsInN1YiI6ImNhNjM5YmI5LTI2YWItNDJlNS1iOWJmLTNhZWEyN2IzMzFmZCJ9.05vGt-_0Mw6WEFJF3jpaqkNb88PUMplsjzlEUvCEfnQ",
    domain: "https://test.cloudflareaccess.com",
  });

  return new Response(`Hello, ${identity.name || "service user"}!`);
};
```

`getIdentity `函数接收一个包含两个属性的对象：一个 `jwt `字符串和一个 `domain `字符串。它会返回一个[由 `/cdn-cgi/access/get-identity` 接口返回的对象](/cloudflare-one/identity/authorization-cookie/application-token/#user-identity)的`Promise`。如果你想使用用户的群组成员身份来获取应用程序权限，这一点尤其有用。

为方便起见，可使用 `data.cloudflareAccess.JWT.getIdentity` 函数获取当前请求的 JWT 的相同信息(假设你已使用上述插件验证了请求)：

```typescript
---
filename: functions/greet.ts
---
import type { PluginData } from "@cloudflare/pages-plugin-cloudflare-access";

export const onRequest: PagesFunction<unknown, any, PluginData> = async ({
  data,
}) => {
  const identity = await data.cloudflareAccess.JWT.getIdentity();

  return new Response(`Hello, ${identity.name || "service user"}!`);
};
```

### 登录和注销 URL

如果要强制登录或注销，可使用这些实用功能生成 URL 并重定向用户：

```typescript
---
filename: functions/login.ts
---
import { generateLoginURL } from "@cloudflare/pages-plugin-cloudflare-access/api";

export const onRequest = () => {
  const loginURL = generateLoginURL({
    redirectURL: "https://example.com/greet",
    domain: "https://test.cloudflareaccess.com",
    aud: "4714c1358e65fe4b408ad6d432a5f878f08194bdb4752441fd56faefa9b2b6f2",
  });

  return new Response(null, {
    status: 302,
    headers: { Location: loginURL },
  });
};
```

```typescript
---
filename: functions/logout.ts
---
import { generateLogoutURL } from "@cloudflare/pages-plugin-cloudflare-access/api";

export const onRequest = () =>
  new Response(null, {
    status: 302,
    headers: {
      Location: generateLogoutURL({
        domain: "https://test.cloudflareaccess.com",
      }),
    },
  });
```