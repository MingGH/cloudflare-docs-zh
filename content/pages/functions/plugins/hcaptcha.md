---
pcx_content_type: concept
title: hCaptcha
weight: 1
---

# hCaptcha 页面插件

hCaptcha 页面插件可验证 hCaptcha 标记。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-hcaptcha
```

## 使用方法

```typescript
---
filename: functions/register.ts
---
import hCaptchaPlugin from "@cloudflare/pages-plugin-hcaptcha";

export const onRequestPost: PagesFunction[] = [
  hCaptchaPlugin({
    secret: "0x0000000000000000000000000000000000000000",
    sitekey: "10000000-ffff-ffff-ffff-000000000001",
  }),
  (async (context) => {
    // Request has been validated as coming from a human

    const formData = await context.request.formData()

    // Store user credentials

    return new Response("Successfully registered!")
  })
];
```

该插件只公开一条路由。无论安装在何处，它都将可用。在上例中，由于该插件被加载到 `functions/register.ts` 中，因此它将验证对 `/register`的请求。该插件使用具有以下属性的单个对象参数加载。

[`secret`](https://dashboard.hcaptcha.com/settings)(必填)和[`sitekey`](https://dashboard.hcaptcha.com/sites)(可选)都可以在 hCaptcha 面板中找到。

`response` 和 `remoteip` 是可选字符串。`response`是要验证的 hCaptcha 标记(默认从 `multipart/form-data` 请求中提取 `h-captcha-response`)。`remoteip` 应该是请求者的 IP 地址(默认为请求的 `CF-Connecting-IP` header)。

`onError `是一个可选函数，它接收页面函数上下文对象并返回一个 `Response `的 `Promise`。默认情况下，它会返回一个人类可读的错误`Response`。

`data.hCaptcha`将在随后的页面函数(包括 `onError `函数)中使用[hCaptcha 响应对象](https://docs.hcaptcha.com/#verify-the-user-response-server-side)。