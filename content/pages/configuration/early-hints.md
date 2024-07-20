---
pcx_content_type: concept
title: Early Hints
---

# Early Hints

[早期提示](/cache/advanced-configuration/early-hints/) 可帮助浏览器更快地加载网页。所有 `pages.dev` 域和自定义域都会自动启用早期提示。

早期提示会自动缓存任何 [`preload`](https://developer.mozilla.org/en-US/docs/Web/HTML/Link_types/preload) 和 [`预连接`](https://developer.mozilla.org/en-US/docs/Web/HTML/Link_types/preconnect) 类型的 [`Link`header](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers/Link) 以作为早期提示发送给浏览器。这些提示是在准备完整响应之前发送给浏览器的，这样浏览器就能知道如何为最终用户更快地加载网页。在页面中创建这些 `Link `header有两种方法：

## 配置早期提示

早期提示可以通过以下两种方法之一创建。

### 1. 配置你的 `_headers` 文件

使用 [`_headers`文件](/pages/configuration/headers/)创建自定义headers。如果在网站的 `/blog/` 部分包含一个特定的样式表，则需要创建以下规则：

```txt
---
filename: _headers
---
/blog/*
  Link: </styles.css>; rel=preload; as=style
```

页面将附加此 `Link: </styles.css>; rel=preload; as=stylesheet` header。一旦缓存，早期提示就会将此标题作为早期提示发出。

### 2. 自动生成 `Link `标题

为了让编写体验更轻松，Pages 还会自动从任何带有以下属性的 `<link>` HTML 元素中生成 `Link` headers：

- `href`
- `as` (optional)
- `rel` (one of `preconnect`, `preload`, or `modulepreload`)

包含任何其他附加属性(例如，`fetchpriority`、`crossorigin `或 `data-do-not-generate-a-link-header`)的`<link>`元素将不会被用于生成 `Link `header，以防止意外丢失任何自定义优先级逻辑，否则这些逻辑将作为早期提示被丢弃。

这样，你就可以在编写文档时直接创建早期提示，而无需交替使用 HTML 和 `_headers`文件。

```html
---
filename: /blog/index.html
---

<html>
  <head>
    <link rel="preload" href="/style.css" as="style" />
    <link rel="stylesheet" href="/style.css" />
  </head>
</html>
```

### 禁用自动生成 `Link `header 自动生成 `Link `header

在 `_headers` 文件中添加以下内容，删除自动生成的 `Link` headers信息：

```txt
---
filename: _headers
---
/*
  ! Link
```

{{<Aside type="warning">}}

自动生成 `Link `header不会对网站性能产生任何负面影响。如果你需要禁用此功能，请通过我们的 [Discord ](https://discord.com/invite/cloudflaredev) 联系我们，告诉我们你的情况。

{{</Aside>}}