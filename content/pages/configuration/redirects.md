---
pcx_content_type: concept
title: 重定向
---

# 重定向

要在 Cloudflare 页面上使用重定向，请在项目输出文件夹中名为 `_redirects`(不含文件扩展名)的纯文本文件中声明重定向。[构建输出文件夹](/pages/configuration/build-configuration/) 是特定于项目的，因此 `_redirects` 文件不应总是位于版本库的根目录中。重定向的更改会在构建时更新到网站上，因此请确保每次更新重定向时都提交并推送文件，以触发新的构建。

{{<Aside type="warning">}}

重定向 "文件中定义的重定向不会应用于由[Functions](/pages/functions/)提供的请求，即使 Function 路由与 URL 模式相匹配。如果你的 Pages 应用程序使用 Functions，则必须将 `_redirects`文件中的任何行为迁移到相应的 `/functions`路由中的代码，或者 [从 Functions 中排除该路由](/pages/functions/routing/#create-a-_routesjson-file)。

{{</Aside>}}

## 结构

### 每行

每行只能定义一个重定向，且必须遵循此格式，否则将被忽略。

```txt
[source] [destination] [code?]
```

{{<definitions>}}

- `source` {{<prop-meta>}}required{{</prop-meta>}}

  - 文件路径。
  - 可包括 [通配符 (`*`)](#splats) 和 [占位符](#placeholders)。
  - 由于片段由浏览器而非 Cloudflare 网络进行评估，因此不会评估源中的任何片段。

- `destination` {{<prop-meta>}}required{{</prop-meta>}}

  - 文件路径或外部链接。
  - 可包括片段、查询字符串、[splats](#splats)和[placeholders](#placeholders)。

- `code` {{<prop-meta>}}default: `302`{{</prop-meta>}}

  - 可选参数

{{</definitions>}}

以 `#` 开头的行将被视为注释。

### 每个文件

一个项目只能有 2,000 个静态重定向和 100 个动态重定向，合计 2,100 个重定向。每个重定向声明有 1000 个字符的限制。

重定向 "文件中：

- 重定向的顺序很重要。如果同一 "源 "路径有多个重定向，则应用最上层的重定向。
- 静态重定向应出现在动态重定向之前。
- 无论资产是否与接收到的请求相匹配，重定向都会被跟踪。

包含多个重定向的完整示例如下：

```txt
---
filename: _redirects
---
/home301 / 301
/home302 / 302
/querystrings /?query=string 301
/twitch https://twitch.tv
/trailing /trailing/ 301
/notrailing/ /nottrailing 301
/page/ /page2/#fragment 301
/blog/* https://blog.my.domain/:splat
/products/:code/:name /products?code=:code&name=:name
```

{{<Aside type= "note">}}

对于某些框架(如 Jekyll)，你可能需要手动复制并粘贴 `_redirects` 文件到构建输出目录。要做到这一点

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 转到 **工作区和页面**> 你的页面项目 > **设置**> **构建和部署**。
3. 转到 **构建配置**> **编辑配置**> 将构建命令改为 `jekyll build && cp _redirects _site/_redirects` 并选择 **保存**。

{{</Aside>}}

## 高级重定向

Cloudflare 目前为高级重定向提供有限的支持。未来将增加更多支持。

{{<table-wrap>}}

| Feature                             | Support | Example                                                         | Notes                                   |
| ----------------------------------- | ------- | --------------------------------------------------------------- | --------------------------------------- |
| Redirects (301, 302, 303, 307, 308) | Yes     | `/home / 301`                                                   | 302 is used as the default status code. |
| Rewrites (other status codes)       | No      | `/blog/* /blog/404.html 404`                                    |                                         |
| Splats                              | Yes     | `/blog/* /posts/:splat`                                         | Refer to [Splats](#splats).             |
| Placeholders                        | Yes     | `/blog/:year/:month/:date/:slug /news/:year/:month/:date/:slug` | Refer to [Placeholders](#placeholders). |
| Query Parameters                    | No      | `/shop id=:id /blog/:id 301`                                    |                                         |
| Proxying                            | Yes     | `/blog/* /news/:splat 200`                                      | Refer to [Proxying](#proxying).         |
| Domain-level redirects              | No      | `workers.example.com/* workers.example.com/blog/:splat 301`     |                                         |
| Redirect by country or language     | No      | `/ /us 302 Country=us`                                          |                                         |
| Redirect by cookie                  | No      | `/\* /preview/:splat 302 Cookie=preview`                        |                                         |

{{</table-wrap>}}

## 重定向和标头匹配

重定向在头文件之前执行，因此如果请求同时符合两个文件中的规则，重定向将胜出。

### Splats

在匹配时，溅射符号(星号，`*`)将贪婪地匹配所有字符。你只能在 URL 中包含一个溅字符。

匹配的值可与 `:splat` 一起用于重定向位置。

### 占位符

{{<render file="_headers_redirects_placeholders.md" withParameters="redirect">}}

```txt
---
filename: _redirects
---
/movies/:title /media/:title
```

### 代理

代理只支持网站上的相对 URL。你不能代理外部域。

只有第一个重定向才适用。例如，在下面的示例中，向 `/a`提出的请求将呈现 `/b`，向 `/b`提出的请求将呈现 `/c`，但 `/a`不会呈现 `/c`。

```
/a /b 200
/b /c 200
```

{{<Aside heading="Proxying's impact on SEO">}}
请注意，代理网页会对搜索引擎优化(SEO)产生不利影响。搜索引擎通常会惩罚提供重复内容的网站。考虑添加一个 `Link` HTTP 标头，告知搜索引擎内容的规范来源。

例如，如果在`_redirects`文件中添加了`/about/faq/* /about/faqs 200`，则可能需要在`_headers`文件中添加以下内容：

```txt
/about/faq/*
  Link: </about/faqs>; rel="canonical"
```

{{</Aside>}}

## 超过`_重定向`限制

一个 [`_redirects`](/pages/platform/limits/#redirects) 文件最多可包含 2,000 个静态重定向和 100 个动态重定向，合计 2,100 个重定向。使用 [Bulk Redirects](/rules/url-forwarding/bulk-redirects/)处理超过 Pages 设置的 2,100 重定向规则限制的重定向。

{{<Aside type="note">}}

在构建文件夹的 `_redirects` 文件中定义的重定向可以与批量重定向一起工作。如果出现重复，批量重定向将在页面项目之前运行，其他重定向也在页面项目中。

例如，如果你设置了批量重定向，将 `abc.com` 指向 `xyz.com`，但同时又设置了 `_redirects`，将 `xyz.com` 指向 `foo.com`，那么对 `abc.com` 的请求最终将重定向到 `foo.com`。

{{</Aside>}}

要使用批量重定向，请参阅 [批量重定向仪表板文档](/rules/url-forwarding/bulk-redirects/create-dashboard/) 或 [批量重定向 API 文档](/rules/url-forwarding/bulk-redirects/create-api/)。

## 相关资源

- [转换规则](/rules/transform/)