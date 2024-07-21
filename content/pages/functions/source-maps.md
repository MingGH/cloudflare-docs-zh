---
pcx_content_type: configuration
title: Source maps and stack traces
meta:
  description: Adding source maps and generating stack traces for Pages.
---
{{<heading-pill style="beta">}}Source maps and stack traces{{</heading-pill>}}

{{<render file="_source-maps.md" productFolder="workers">}}

{{<Aside type="warning">}}

页面源地图上传支持现已开放测试。所需的最低 Wrangler 版本：3.60.0。

{{</Aside>}}

## 源映射

要启用源映射，请在 [`wrangler pages deploy`](/workers/wrangler/commands/#deploy-1) 中提供`--upload-source-maps`标记，或者在 Pages 应用程序的 [`wrangler.toml`](/pages/functions/wrangler-configuration/)文件中添加以下内容(如果使用的是 Pages 编译环境)：

```toml
upload_source_maps = true
```

启用上传源映射后，Wrangler 会在运行 [`wrangler pages deploy`](/workers/wrangler/commands/#deploy-1) 时自动生成并上传源映射文件。

## 堆栈跟踪

当应用程序抛出未捕获异常时，我们会获取源映射，并利用它将异常的堆栈跟踪映射回应用程序的原始源代码行。

然后，你就可以在流式传输 [实时日志](/pages/functions/debugging-and-logging/) 时查看堆栈跟踪。

{{<Aside type="note">}}

源映射会在页面函数调用完成后被检索，这是一个异步过程，不会影响应用程序的 CPU 利用率或性能。源映射在运行时无法在应用程序内部访问，因此如果你使用 [stack property](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Error/stack) `console.log()`，你将不会获得经过去混淆处理的堆栈跟踪。

{{</Aside>}}

## 相关资源

* [实时日志](/pages/functions/debugging-and-logging/) - 了解如何实时捕捉 Pages 日志。