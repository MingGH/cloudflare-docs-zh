---
updated: 2022-07-26
difficulty: Beginner
pcx_content_type: tutorial
title: 从 Netlify 迁移到 Pages
---

# 从 Netlify 迁移到 Pages

在本教程中，你将学习如何将 Netlify 应用程序迁移到 Cloudflare Pages。

## 查找构建命令和构建目录

要将你的应用程序移至 Cloudflare Pages，请查找你的构建命令和构建目录。Cloudflare Pages 将使用这些信息来构建和部署你的应用程序。

在 Netlify 控制面板中，找到要部署的项目。它应配置为从 GitHub 仓库部署。

![在 Netlify 控制面板中选择站点](/images/pages/migrations/netlify-deploy-1.png)

在网站仪表板内，选择**网站设置**，然后选择**构建和部署**。

![在站点仪表板中选择站点设置](/images/pages/migrations/netlify-deploy-2.png)

![在侧边栏中选择 `构建和部署`](//images/pages/migrations/netlify-deploy-3.png)

在 `构建和部署 `**选项卡中，找到 `构建设置 `**面板，其中有 `构建命令 `**和 `发布目录 `**字段。保存这些字段，以便部署到 Cloudflare 页面。在下图中，**构建命令**为 `yarn build`，**发布目录**为 `build/`。

![查找构建命令和发布目录字段](/images/pages/migrations/netlify-deploy-4.png)

## 迁移重定向和header

如果你的网站在发布目录中包含一个 `_redirects` 文件，你可以在 Cloudflare Pages 中使用相同的文件，重定向将成功执行。如果你的重定向位于 `netlify.toml` 文件中，则需要将其添加到 `_redirects` 文件夹中。Cloudflare Pages 目前提供有限的 [高级重定向支持](/pages/configuration/redirects/)。如果你有超过 2000 个静态和/或 100 个动态重定向规则，建议使用 [批量重定向](/rules/url-forwarding/bulk-redirects/create-dashboard/)。

你也可以将头文件移入发布目录中的 `_headers` 文件夹。需要注意的是，在 `_headers`文件中定义的自定义页眉目前并不适用于函数的响应，即使函数路由与 URL 模式相匹配也是如此。要了解有关如何 [处理标题](/pages/configuration/headers/)的更多信息，请参阅 [标题](/pages/configuration/headers/)。

{{<Aside type="note">}}

重定向先于headers执行。如果请求同时符合两个文件中的规则，则重定向优先。

{{</Aside>}}

## 表格

在表单组件中，删除 `data-netlify = "true"` 属性或 `<form>` 标记中的 Netlify 属性。现在，你可以将表单逻辑作为 Pages 函数，并将条目收集到数据库或 Airtable 中。更多信息，请参阅 [使用 Pages 函数处理表单提交](/pages/tutorials/forms/) 教程。

## 无服务器功能

Netlify 函数和 Pages 函数共享相同的文件系统惯例，即在项目基础中使用 `functions `目录来处理无服务器函数。不过，语法和部署函数的方式有所不同。页面功能在 Cloudflare Worker 上运行，默认情况下在 Cloudflare 全球网络上运行，部署时不需要任何额外的代码或配置。

Cloudflare Pages Functions 还提供中间件，可处理你需要在函数路由处理程序之前和/或之后运行的任何逻辑。

### 函数语法

Netlify 函数导出一个异步事件处理程序，接受事件和上下文作为参数。在页面函数的情况下，你必须导出一个接受 `context` 对象的 `onRequest` 函数。`context `对象包含请求的所有信息，如 `request`、`env `和 `params`，并返回一个新的 Response。了解有关 [编写第一个函数](/pages/functions/get-started/) 的更多信息

使用 Netlify 函数的 Hello World：

```js
exports.handler = async function (event, context) {
  return {
    statusCode: 200,
    body: JSON.stringify({ message: "Hello World" }),
  };
}

```

使用页面函数的 Hello World

```js
export async function onRequestPost(request) {
  return new Response(`Hello world`);
}
```

## 其他 Netlify 配置

你的 `netlify.toml` 文件可能包含 Pages 支持的其他配置，如预览部署、指定发布目录和插件。迁移配置后，你可以删除该文件。


## 访问管理

你可以将访问管理迁移到 [Cloudflare Zero Trust](/cloudflare-one/)，它允许你管理应用程序、事件日志和请求的用户身份验证。

## 创建一个新的 Pages 项目

找到构建目录和构建命令后，就可以将项目移动到 Cloudflare 页面。

[入门指南](/pages/get-started/) 将指导你如何将 GitHub 项目添加到 Cloudflare Pages。

如果你选择为你的 Pages 使用自定义域，你可以将其设置为与你当前部署的 Netlify 应用程序相同的自定义域。要为 Pages 项目分配自定义域，请参阅 [自定义域](/pages/configuration/custom-domains/)。

## 清理旧应用程序并分配域名

在 Cloudflare 控制面板中，转到 **DNS**> **Records**，查看你是否已将域名的 CNAME 记录从 Netlify 更新到 Cloudflare Pages。更新 DNS 记录后，请求将转到你的 Pages 应用程序。

在**DNS**中，你的记录的**内容**应该是你的`<SUBDOMAIN>.pages.dev `子域。

完成上述步骤后，你已成功将 Netlify 项目迁移到 Cloudflare Pages。