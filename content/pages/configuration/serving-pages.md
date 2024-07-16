---
pcx_content_type: concept
title: Serving Pages
---

# 服务页面

Cloudflare Pages 包含一系列为你的 Pages 站点提供服务的默认设置。本页将详细介绍其中的一些决定，以便你了解 Pages 的工作方式，以及你可能希望如何覆盖某些默认行为。

## 路线匹配

如果找到的 HTML 文件路径与请求的当前路由相匹配，Pages 就会提供该文件。Pages 还会将 HTML 页面重定向到无扩展名的对应页面：例如，`/contact.html` 将重定向到 `/contact`，`/about/index.html` 将重定向到 `/about/`。

## 未找到行为

你可以通过创建一个 `404.html` 文件来定义一个自定义页面，以便在 Pages 无法找到请求的文件时显示。然后，Pages 会尝试查找最近的 404 页面。如果在与当前请求的路径相同的目录中找不到，它将继续在目录树中查找匹配的`404.html`文件，以`/404.html`结尾。这意味着你可以为 `/blog/404.html` 和 `/404.html` 等情况定义自定义 404 路径，Pages 会根据情况自动呈现正确的路径。

## 单页应用程序(SPA)渲染

如果你的项目不包含顶层的 `404.html` 文件，Pages 会假定你部署的是单页面应用程序。这包括 React、Vue 和 Angular 等框架。Pages 的默认单页面应用程序行为会将所有传入路径匹配到根(`/`)，因此你可以捕获诸如`/about`或`/help`之类的 URL，并在你的 SPA 中对其做出响应。

## 缓存和性能

### 建议

在大多数情况下，应避免在网站上设置任何自定义缓存。Pages 自带内置缓存默认设置，可尽可能优化缓存，同时提供最新内容。每次将资产部署到 Pages 时，该资产都会一直缓存在 Cloudflare CDN 上，直到下一次部署。

因此，如果在 [自定义域](/pages/configuration/custom-domains/) 中添加缓存，可能会导致在部署后提供陈旧的资产。

此外，在自定义域中添加缓存可能会导致[页面重定向](/pages/configuration/redirects/) 或[页面功能](/pages/functions/) 出现问题。之所以会出现这些问题，是因为缓存的响应可能会在 Pages 对请求采取行动之前提供给最终用户。

不过，在某些情况下，自定义域上的[缓存规则](/cache/how-to/cache-rules/)确实很有意义。例如，你可以轻松缓存不可变资产的位置，如文件名中包含内容哈希值的 CSS 或 JS 文件。在这种情况下，自定义缓存可以加快用户体验，直到文件(和相关文件名)发生变化。但要确保缓存不会干扰任何重定向或功能。

请注意，分层缓存不支持页面上的自定义域。

{{<Aside type="note" header="Purging the cache">}}

如果你发现新部署后提供的是过期资产，请转到你的区域，然后**缓存**> **配置**> [**清除一切**](/cache/how-to/purge-cache/purge-everything/)，以确保提供最新的部署。

{{</Aside>}}

### 行为

为了便于浏览器缓存，Pages 会始终为 `200 OK`响应发送 `Etag`头，然后浏览器会在对该资产的后续请求中以 `If-None-Match`头返回。Pages 会将请求中的  `If-None-Match`头与计划发送的 `Etag` 头进行比较，如果两者匹配，Pages 就会响应一个 `304 Not Modified`(未修改)，告诉浏览器可以安全地使用本地缓存中存储的内容。

目前，页面会对 HTTP 范围请求返回  `200`响应；不过，团队正在努力添加符合规范的 `206`部分响应。

页面还将尽可能提供 Gzip 和 Brotli 响应。

## 资产保留

我们将根据每个数据中心的情况将资产插入缓存。资产的存活时间 (TTL) 为一周，但也可能随时消失。如果进行新的部署，资产在该数据中心的存在时间可能长达一周。

## header

默认情况下，Pages 在提供资产时会自动添加多个 [HTTP 响应header](https://developer.mozilla.org/en-US/docs/Glossary/Response_header)，包括

```txt
---
header: Headers always added
---
Access-Control-Allow-Origin: *
Cf-Ray: $CLOUDFLARE_RAY_ID
Referrer-Policy: strict-origin-when-cross-origin
Etag: $ETAG
Content-Type: $CONTENT_TYPE
X-Content-Type-Options: nosniff
Server: cloudflare
```

{{<Aside type="note">}}

Cf-Ray](/fundamentals/reference/cloudflare-ray-id/)header对 Cloudflare 来说是唯一的。

{{</Aside>}}

```txt
---
header: Headers sometimes added
---

// if the asset has been encoded
Cache-Control: no-transform
Content-Encoding: $CONTENT_ENCODING

// if the asset is cacheable (the request does not have an `Authorization` or `Range` header)
Cache-Control: public, max-age=0, must-revalidate

// if requesting the asset over a preview URL
X-Robots-Tag: noindex
```

要修改 Cloudflare Pages 添加的header(也许要添加 [Early Hints](/pages/configuration/early-hints/))，请更新项目中的 [_headers 文件](/pages/configuration/headers/)。