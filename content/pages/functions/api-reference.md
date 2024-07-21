---
pcx_content_type: reference
title: 接口参考
weight: 3
meta:
  description: 了解 Pages Functions 中使用的 API。
---

# 接口参考

可以使用以下方法配置页面功能。

## 方法

### `onRequests`(关于请求

{{<definitions>}}

- {{<code>}}onRequest(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 无论请求方法如何，所有请求都将调用该函数。
- {{<code>}}onRequestGet(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 所有 `GET` 请求都将调用该函数。
- {{<code>}}onRequestPost(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 所有 `POST` 请求都将调用该函数。
- {{<code>}}onRequestPatch(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 所有 `PATCH` 请求都将调用该函数。
- {{<code>}}onRequestPut(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 所有 `PUT` 请求都将调用该函数。
- {{<code>}}onRequestDelete(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 所有 `DELETE` 请求都将调用该函数。
- {{<code>}}onRequestHead(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 所有 `HEAD` 请求都将调用该函数。
- {{<code>}}onRequestOptions(context[{{<param-type>}}EventContext{{</param-type>}}](#eventcontext)){{</code>}} {{<type>}}Response | Promise&lt;Response&gt;{{</type>}}
  - 所有 `OPTIONS` 请求都将调用该函数。

{{</definitions>}}

### `env.ASSETS.fetch()`

通过 `env.ASSETS.fetch()` 函数，你可以从 Pages 项目中获取静态资产。

你可以向`env.ASSETS.fetch()`函数传递[请求对象](/workers/runtime-apis/request/)、URL 字符串或 URL 对象。URL 必须指向漂亮路径，而不是直接指向资产。例如，如果你的路径是 `/users/index.html`，你将请求 `/users/`，而不是 `/users/index.html`。此方法调用将运行头和重定向规则，修改返回的响应。

## 类型

### `EventContext` 事件上下文

{{<definitions>}}
以下是在 `onRequest` 方法中传递的 `context` 对象的属性：

  - `request` [{{<type>}}Request{{</type>}}](/workers/runtime-apis/request/)

      这是传入的 [请求](/workers/runtime-apis/request/)。

  - `functionPath` {{<type>}}string{{</type>}}

      这是请求的路径。

  - {{<code>}}waitUntil(promise{{<param-type>}}Promise&lt;any&gt;{{</param-type>}}){{</code>}} {{<type>}}void{{</type>}}

      更多信息请参阅 [`waitUntil`文档](/workers/runtime-apis/context/#waituntil)。


  - {{<code>}}passThroughOnException(){{</code>}} {{<type>}}void{{</type>}}

      更多信息请参阅 [`passThroughOnException`文档](/workers/runtime-apis/context/#passthroughonexception)。请注意，这将无法在 [advanced mode project](/pages/functions/advanced-mode/) 上运行。

  - {{<code>}}next(input?{{<param-type>}}Request | string{{</param-type>}}, init?{{<param-type>}}RequestInit{{</param-type>}}){{</code>}} {{<type>}}Promise&lt;Response&gt;{{</type>}}

      将请求传递到下一个功能，如果没有其他功能，则传递到资产服务器。

  - `env` [{{<type>}}EnvWithFetch{{</type>}}](#envwithfetch)
  - `params` {{<type>}}Params&lt;P&gt;{{</type>}}

      保存 [dynamic routing](/pages/functions/routing/#dynamic-routes) 中的值。

      在下面的示例中，动态路径为 `/users/[user].js`。当你访问 `/users/nevi`上的网站时，`params`对象将显示如下内容：

      ```js
      {
        user: "nevi"
      }
      ```

      这样就可以从路径中获取动态值：

      ```js
      export function onRequest(context) {
        return new Response(`Hello ${context.params.user}`);
      }
      ```

      Which would return `"Hello nevi"`.

  - `data` {{<type>}}Data{{</type>}}

{{</definitions>}}

### `EnvWithFetch`.

保存函数的环境变量、秘密和绑定。它还保存了 `ASSETS` 绑定，这样就可以回退到资产服务行为。