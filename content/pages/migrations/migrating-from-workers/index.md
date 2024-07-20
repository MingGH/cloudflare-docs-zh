---
updated: 2020-08-13
difficulty: Beginner
pcx_content_type: tutorial
title: 从Workers迁移到Pages
---

# 从Workers迁移到Pages

在本教程中，你将学习如何将现有的 [Cloudflare Workers Sites](https://workers.cloudflare.com/sites) 应用程序迁移到 Cloudflare Pages。

前提条件是，你应该有一个使用 [Wrangler](https://github.com/cloudflare/workers-sdk/tree/main/packages/wrangler) 创建的 Cloudflare Workers Sites 项目。

Cloudflare Pages 为服务网站的各个方面提供内置默认设置。你可以使用 [Functions](/pages/functions/)将 Worker 中的自定义行为(如自定义缓存逻辑)移植到 Cloudflare Pages 项目中。这样就可以实现一个易于使用、基于文件的路由系统。你还可以将自定义标题和重定向迁移到 Pages。

你可能已经有了一个相当复杂的 Worker，而且，或者说将其拼接到 Pages 基于文件的路由系统中会很繁琐。在这种情况下，Pages 为开发人员提供了在 Pages 项目的输出目录中定义一个 `_worker.js` 文件的功能。

{{<Aside type="note">}}

使用 `_worker.js` 文件时，整个 `/functions` 目录都将被忽略 - 这包括其路由和中间件特性。相反，`_worker.js` 文件将按原样部署，并且必须使用 [Module Worker 语法](/workers/reference/migrate-to-module-workers/)编写。

{{</Aside>}}

迁移到 Cloudflare Pages 后，你将能够访问 [预览部署](/pages/configuration/preview-deployments/) 和自动分支部署等功能，无需额外配置。

## 删除不必要的代码

工人遗址项目由以下部分组成：

1. 使用[静态网站工具](/pages/how-to/)或 HTML、CSS 和 JavaScript 文件的静态集合构建的应用程序。
2. 如果使用静态网站工具，则需要一个构建目录(在 `wrangler.toml` 中称为`bucket`)，静态项目会在此构建 HTML、CSS 和 JavaScript 文件。
3. 为构建目录提供服务的 Worker 应用程序。对于大多数项目来说，这可能是 `workers-site` 目录。

迁移到 Cloudflare Pages 时，请删除 Workers 应用程序和任何相关的 `wrangler.toml` 配置文件或构建输出。相反，请注意并记录你的 `build` 命令(如果有的话)，以及项目目录中 `wrangler.toml` 文件中的 `bucket` 字段或构建目录。

## 迁移header和重定向

你可以在输出目录中创建一个 `_redirects` 文件，将重定向迁移到 Pages。Pages 目前对高级重定向的支持有限。未来将添加更多支持。有关支持类型的列表，请参阅[重定向文档](/pages/configuration/redirects/)。

{{<Aside type="note">}}

一个项目只能有 2,000 个静态重定向和 100 个动态重定向，合计 2,100 个重定向。每个重定向声明有 1000 个字符的限制。畸形定义将被忽略。如果同一源路径有多个重定向，则应用最上层的重定向。

确保在 `_redirects` 文件中，静态重定向位于动态重定向之前。

{{</Aside>}}

除 `_redirects` 文件外，Cloudflare 还提供 [批量重定向](/pages/configuration/redirects/#surpass-_redirects-limits)，用于处理超过 Pages 设置的 2,100 条重定向规则限制的重定向。

也可以将自定义标题移入输出目录中的 `_headers` 文件。需要注意的是，在 `_headers`文件中定义的自定义header目前不会应用于函数的响应，即使函数路由与 URL 模式相匹配。要了解有关处理标题的更多信息，请参阅 [标题](/pages/configuration/headers/)。


## 创建一个新的 Pages 项目

### 连接到你的 Git 提供商

将 **build 命令**和 **build 目录**记录到一个单独的位置后，删除应用程序中的所有其他内容，并将项目的新版本推送到 git 提供商。按照 [入门指南](/pages/get-started/) 使用你之前保存的 **build 命令**和 **build 目录**，将你的项目添加到 Cloudflare Pages。

如果选择在 Pages 项目中使用自定义域，可以将其设置为与当前部署的 Workers 应用程序相同的自定义域。请按照 [添加自定义域](/pages/configuration/custom-domains/#add-a-custom-domain) 的步骤操作。

{{<Aside type="note">}}

部署前，你需要删除旧的 Workers 路由，以便开始向 Cloudflare 页面发送请求。

{{</Aside>}}

### 使用直接上传

如果你的 Workers 网站有自定义构建设置，你可以使用 [Direct Upload](/pages/get-started/direct-upload/)将预构建资产导入页面。此外，你还可以使用[Wrangler CLI](/workers/wrangler/install-and-update/)或拖放选项，将网站资产直接传送到 Cloudflare 全球网络。

通过这些选项，你可以从 CLI 或仪表板创建新项目并为其命名。项目部署完成后，你可以按照 Pages 项目中的 [添加自定义域](/pages/configuration/custom-domains/#add-a-custom-domain) 步骤设置自定义域。

## 清理旧应用程序并分配域名

部署完 Pages 应用程序后，删除 Worker：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 进入**工作者和页面**，在**概览**中选择你的工作者。
3. 转到 **管理服务**> **删除**。

删除 Workers 应用程序后，请求将转至 Pages 应用程序。通过完成本指南，你已成功将 Workers Sites 项目迁移到 Cloudflare Pages。