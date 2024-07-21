---
pcx_content_type: concept
title: Static Forms
weight: 1
---

# 静态表单页面插件

静态表单页面插件可拦截所有设置了 `data-static-form-name `属性的表单提交。这样，你就可以对这些表单提交采取行动，例如，将提交保存到 KV。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-static-forms
```

## 使用方法

```typescript
---
filename: functions/_middleware.ts
---
import staticFormsPlugin from "@cloudflare/pages-plugin-static-forms";

export const onRequest: PagesFunction = staticFormsPlugin({
  respondWith: ({ formData, name }) => {
    const email = formData.get('email')
    return new Response(`Hello, ${email}! Thank you for submitting the ${name} form.`)
  }
});
```

```html
---
filename: public/sales-enquiry.html
---
<body>
  <h1>Sales enquiry</h1>
  <form data-static-form-name="sales">
    <label>Email address <input type="email" name="email" /></label>
    <label>Message <textarea name="message"></textarea></label>
    <button type="submit">Submit</button>
  </form>
</body>
```

该插件只接受一个参数，即带有 `respondWith` 属性的对象。该函数接收一个具有 `formData` 属性([`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData) 实例)和 `name` 属性(你的 `data-static-form-name` 属性的名称值)的对象。它应返回 `Response` 或 `Response` 的 `Promise`。正是在这个 `respondWith` 函数中，你可以执行一些操作，例如序列化 `formData` 并将其保存到 KV 命名空间。

HTML 表单的 `method `和 `action `属性无需设置。插件会自动覆盖它们，以便拦截提交。