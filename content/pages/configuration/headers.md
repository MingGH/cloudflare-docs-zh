---
pcx_content_type: concept
title: Headers
---

# Headers

## Attach a header

要将headers附加到 Cloudflare Pages 响应，请在项目的输出文件夹中创建一个 `_headers` 纯文本文件。该文件夹通常包含部署就绪的 HTML 文件和由构建生成的资产(如 favicons)。对头文件的修改会在构建时更新到网站上。确保每次更新headers时都提交并推送文件，以触发新的构建。

{{<Aside type="warning">}}

在 `_headers`文件中定义的自定义标题不会应用于来自[Functions](/pages/functions/)的响应，即使 Function 路由与 URL 模式相匹配。如果你的 Pages 应用程序使用 "函数"，则必须将`_headers`文件中的任何行为迁移到相应`/functions`路由中的 `Response`对象。在更改多个路由的头文件时，你可能会对用于共享行为的 [添加中间件](/pages/functions/middleware/) 感兴趣。

{{</Aside>}}

标题规则以多行块的形式定义。块的第一行是应应用规则标题的 URL 或 URL 模式。在下一行，必须写入标题名称和标题值的缩进列表：

```txt
[url]
  [name]: [value]
```

支持使用绝对 URL，但请注意绝对 URL 必须以 `https` 开头，且不支持指定端口。Cloudflare Pages 在匹配传入请求时会忽略传入请求的端口和协议。例如，"https://example.com/path "这样的规则将匹配到 "other://example.com:1234/path "的请求。

你可以在随后的行中定义任意多的 `[name]: [value]`对。例如

```txt
---
filename: _headers
---
# This is a comment
/secure/page
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: no-referrer

/static/*
  Access-Control-Allow-Origin: *
  X-Robots-Tag: nosnippet

https://myproject.pages.dev/*
  X-Robots-Tag: noindex
```

符合多个规则 URL 模式的传入请求将继承所有规则的标题。使用之前的 `_headers` 文件，以下请求将应用以下headers：

| Request URL                                     | Headers                                                                                                                               |
| ----------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------- |
| `https://custom.domain/secure/page`             | `X-Frame-Options: DENY` <br /> `X-Content-Type-Options: nosniff ` <br /> `Referrer-Policy: no-referrer`                               |
| `https://custom.domain/static/image.jpg`        | `Access-Control-Allow-Origin: *` <br /> `X-Robots-Tag: nosnippet`                                                                     |
| `https://myproject.pages.dev/home`              | `X-Robots-Tag: noindex`                                                                                                               |
| `https://myproject.pages.dev/secure/page`       | `X-Frame-Options: DENY` <br /> `X-Content-Type-Options: nosniff` <br /> `Referrer-Policy: no-referrer` <br /> `X-Robots-Tag: noindex` |
| `https://myproject.pages.dev/static/styles.css` | `Access-Control-Allow-Origin: *` <br /> `X-Robots-Tag: nosnippet, noindex`                                                            |

一个项目仅限 100 条标题规则。文件 `_headers`中的每一行有 2,000 个字符的限制。整行(包括间距、页眉名称和值)都计入此限制。

如果一个header在 `_headers`文件中应用了两次，则这些值会用逗号分隔。在`_headers`文件中定义的header会覆盖 Cloudflare Pages 通常发送的内容，因此在设置安全header时要注意。Cloudflare 保留随时为 Pages 项目附加新header的权利，以提高性能或加强部署的安全性。

### 分离header

你可能希望删除由更普遍的规则添加的header。这可以通过添加感叹号来实现。

```txt
---
filename: _headers
---
/*
  Content-Security-Policy: default-src 'self';

/*.jpg
  ! Content-Security-Policy
```

### 匹配路径

与 [`_redirects`](/pages/configuration/redirects/) 所提供的 URL 匹配功能相同，`_headers`文件也可以使用。但要注意的是，重定向会在头文件之前应用，当请求同时匹配重定向和头文件时，重定向优先。

#### Splats

匹配时，星号 (`*`) 表示的 splat 模式会贪婪地匹配所有字符。你只能在 URL 中包含一个 splat。

匹配的值可在标题值中作为 `:splat` 占位符引用。

#### 占位符

{{<render file="_headers_redirects_placeholders.md" withParameters="header">}}

```txt
---
filename: _headers
---
/movies/:title
  x-movie-name: You are watching ":title"
```

## 示例

### 跨源资源共享(CORS)

为使其他域也能从你的 Pages 项目中获取每个资产，可在`_headers`文件中添加以下内容：

```txt
---
filename: _headers
---
/*
  Access-Control-Allow-Origin: *
```

这将把 `Access-Control-Allow-Origin` header应用到任何传入的 URL。为了限制更严格，你可以定义一个适用于 `*.pages.dev` 子域的 URL 模式，这样就只允许从其 `staging` 分支的子域进行访问：

```txt
---
filename: _headers
---
https://:project.pages.dev/*
  Access-Control-Allow-Origin: https://staging.:project.pages.dev/
```

### 防止你的 pages.dev 部署显示在搜索结果中

[Google](https://developers.google.com/search/docs/advanced/robots/robots_meta_tag#directives)和其他搜索引擎通常支持 "X-Robots-Tag "header，以指示爬虫如何索引你的网站。

例如，要防止你的 `*.pages.dev` 部署被索引，请在你的 `_headers` 文件中添加以下内容：

```txt
---
filename: _headers
---
https://:project.pages.dev/*
  X-Robots-Tag: noindex
```

### 加强应用程序的安全性

{{<Aside type="note">}}

如果你使用 Pages Functions 并希望附加安全header以控制服务器端逻辑的访问或浏览器行为，则应从 Pages Functions 的 `Response` 文件而不是 `_headers` 文件中发送header。例如，如果你有一个 API 端点，并希望允许跨源请求，则应确保 Pages Functions 将 CORS header附加到其响应中，包括附加到 `OPTIONS` 请求中。这样做是为了确保在万一发生涉及提供静态资产的事件时，你的 API 安全header仍可继续配置。

{{</Aside>}}

你可以通过[`X-Frame-Options`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Frame-Options) header通知浏览器不要将你的应用程序嵌入到另一个应用程序中(例如，使用`<iframe>`)，从而防止点击劫持。

[`X-Content-Type-Options:nosniff`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/X-Content-Type-Options)可防止浏览器将响应解释为任何其他内容类型，而不是 `Content-Type` header所定义的内容类型。

[`Referrer-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Referrer-Policy)可让你自定义访客在浏览你的网页时提供多少有关他们来自哪里的信息。

浏览器功能可通过[`Permissions-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Feature-Policy)header(最近由 `Feature-Policy` 更名而来)在不同程度上禁用。

如果你需要对应用程序的内容进行精细控制，[`Content-Security-Policy`](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Content-Security-Policy) header允许你配置一系列安全设置，包括与`X-Frame-Options` header类似的控制。

```txt
---
filename: _headers
---
/app/*
  X-Frame-Options: DENY
  X-Content-Type-Options: nosniff
  Referrer-Policy: no-referrer
  Permissions-Policy: document-domain=()
  Content-Security-Policy: script-src 'self'; frame-ancestors 'none';
```