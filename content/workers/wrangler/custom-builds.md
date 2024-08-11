---
pcx_content_type: configuration
title: Custom builds
meta:
  description: Customize how your code is compiled, before being processed by Wrangler.
---

# 自定义构建

自定义构建是一种方法可让你自定义代码在被 Wrangler 处理之前的编译方式。

{{<Aside type="note">}}
随着 Wrangler v2 的发布，不再需要使用自定义构建来通过 webpack 和类似的捆绑程序捆绑代码。作为 `dev` 和 `publish` 命令的一部分，Wrangler 默认运行 [esbuild](https://esbuild.github.io/)，并将 Worker 项目捆绑到一个 Worker 脚本中。请参阅 [捆绑](/workers/wrangler/bundling/)。
{{</Aside>}}

## 配置自定义构建

在 `wrangler.toml` 中添加`[构建]`部分，并使用以下选项配置自定义构建，即可配置自定义构建。

{{<definitions>}}

- `command` {{<type>}}string{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 用于构建 Worker 的命令。在 Linux 和 macOS 中，该命令在 `sh` shell 中执行，在 Windows 中则在 `cmd` shell 中执行。可以使用 `&&` 和 `||` shell 操作符。该命令将作为 `wrangler dev` 和 `npx wrangler deploy` 的一部分运行。

- `cwd` {{<type>}}string{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 执行命令的目录。

- `watch_dir` {{<type>}}string | string[]{{</type>}} {{<prop-meta>}}optional{{</prop-meta>}}

  - 使用 `wrangler dev` 时要注意更改的目录。默认为当前工作目录。

{{</definitions>}}

例如

```toml
---
header: wrangler.toml
---
[build]
command = "npm run build"
cwd = "build_cwd"
watch_dir = "build_watch_dir"
```