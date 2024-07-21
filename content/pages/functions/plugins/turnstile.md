---
pcx_content_type: concept
title: Turnstile
weight: 1
---

# Turnstile页面插件

[Turnstile](/turnstile/)是 Cloudflare 的智能验证码替代方案。

Turnstile Pages 插件可验证 Cloudflare Turnstile 标记。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-turnstile
```

## 使用方法

```typescript
---
filename: functions/register.ts
---
import turnstilePlugin from "@cloudflare/pages-plugin-turnstile";

/**
 * POST /api/submit-with-plugin
 */

export const onRequestPost = [
        turnstilePlugin({
        // This is the demo secret key. In prod, we recommend you store
        // your secret key(s) safely.
        secret: "0x4AAAAAAASh4E5cwHGsTTePnwcPbnFru6Y",
        }),
        // Alternatively, this is how you can use a secret key which has been stored as an environment variable
        // (async (context) => {
        //   return turnstilePlugin({secret: context.env.SECRET_KEY})(context)
        // }),
        (async (context) => {
          // Request has been validated as coming from a human
          const formData = await context.request.formData()
          // Additional solve metadata data is available at context.data.turnstile
          return new Response(`Successfully verified! ${JSON.stringify(context.data.turnstile)}`)
        })
];
```


本插件仅提供一条路由，以验证作为 `cf-turnstile-response` 参数的 `POST` 中传入的 Turnstile 响应。无论安装在何处，它都将可用。在上面的示例中，它被安装在 `functions/register.ts`中。因此，它将验证对 `/register`的请求。

## 属性

安装插件时只需一个对象参数，该参数具有以下属性：

[`secret`](https://dash.cloudflare.com/login)是必填项，都可以在 Turnstile 面板中找到。

`response` 和 `remoteip` 是可选字符串。`response` 是要验证的 Turnstile 标记。如果未提供，插件将默认从 `multipart/form-data` 请求中提取 `cf-turnstile-response` 值)。`remoteip` 是请求者的 IP 地址。默认为请求的 `CF-Connecting-IP` header。

`onError `是一个可选函数，它接收页面函数上下文对象并返回一个 `Response `的 `Promise`。默认情况下，它会返回一个人类可读的错误`Response`。

`context.data.turnstile `将在后续的页面函数(包括 `onError `函数)中使用[Turnstile siteverify 响应对象](/turnstile/get-started/server-side-validation/)进行填充。