---
pcx_content_type: how-to
title: Add custom HTTP headers
---

# 添加自定义 HTTP header

{{<Aside type="note">}}

Cloudflare 通过在你的项目中添加`_headers`文件为 Pages 项目提供 HTTP header自定义功能。有关详细信息，请参阅 [文档](/pages/configuration/headers/)。

{{</Aside>}}

通过 Cloudflare Workers [serverless functions](https://www.cloudflare.com/learning/serverless/what-is-serverless/)，可以对 HTTP header进行更高级的定制。

如果之前没有部署过 Worker，请从我们的 [教程](/workers/get-started/guide/)开始。在本教程中，请先完成第一步(注册 Worker 账户)到第四步(生成新项目)，然后再返回本页。

在继续之前，请确保你的 Cloudflare 页面项目已连接到 [自定义域](/pages/configuration/custom-domains/#add-a-custom-domain)。

## 编写一个 Workers 函数

Worker 功能使用 [JavaScript](https://www.cloudflare.com/learning/serverless/serverless-javascript/) 编写。当 Worker 向 Cloudflare Pages 应用程序发出请求时，它会收到一个响应。Worker 收到的响应是不可变的，即无法更改。要添加、删除或更改header，请克隆响应并在新的 `Response` 实例上修改header。将新的响应返回给浏览器，并进行所需的header更改。下面是一个示例：

```js
---
header: Setting custom headers with a Workers function
---
export default {
  async fetch(request) {
  // This proxies your Pages application under the condition that your Worker script is deployed on the same custom domain as your Pages project
  const response = await fetch(request);

  // Clone the response so that it is no longer immutable
  const newResponse = new Response(response.body, response);

  // Add a custom header with a value
  newResponse.headers.append('x-workers-hello', 'Hello from Cloudflare Workers');

  // Delete headers
  newResponse.headers.delete('x-header-to-delete');
  newResponse.headers.delete('x-header2-to-delete');

  // Adjust the value for an existing header
  newResponse.headers.set('x-header-to-change', 'NewValue');

  return newResponse;
  }
};
```

## 在仪表板中部署 Workers 功能

开始部署 Workers 功能的最简单方法是在浏览器中键入 [workers.new](https://workers.new/)。登录你的账户，系统会自动引导你进入 Workers & Pages 面板。在 "Workers & Pages "控制面板中，编写你的函数或使用 [Workers 文档中的示例](/workers/examples/)。

脚本准备就绪后，选择**保存并部署**，然后在域的区域设置中设置一个 [路由](/workers/configuration/routing/routes/)。

例如，[这里有一个 Workers 脚本](/workers/examples/security-headers/)，你可以复制并粘贴到 Workers 面板，只要有请求访问你的页面 URL，它就会设置常用的安全header，如 X-XSS-Protection、X-Frame-Options、X-Content-Type-Options、Strict-Transport-Security、Content-Security-Policy (CSP) 等。

## 使用 CLI 部署 Workers 函数

如果你不想自己编写此文件，可以使用我们的 `custom-headers-example` [template](https://github.com/kristianfreeman/custom-headers-example) 通过 Workers CLI 工具 [wrangler](/workers/wrangler/install-and-update/) 生成一个新的 Workers 函数。

```sh
---
header: Generating a serverless function with wrangler
---
$ git clone https://github.com/cloudflare/custom-headers-example
$ cd custom-headers-example
$ npm install
```

要在运行 Pages 应用程序的同时运行 Workers 功能，请将其部署到与 Pages 应用程序相同的自定义域中。为此，请使用账户和区域详细信息更新项目中的 `wrangler.toml` 文件：

```toml
---
filename: wrangler.toml
highlight: [4,6,7]
---
name = "custom-headers-example"

account_id = "FILL-IN-YOUR-ACCOUNT-ID"
workers_dev = false
route = "FILL-IN-YOUR-WEBSITE.com/*"
zone_id = "FILL-IN-YOUR-ZONE-ID"
```

如果你不知道如何查找账户 ID 和区域 ID，请参阅 [我们的指南](/fundamentals/setup/find-account-and-zone-ids/)。

配置好 `wrangler.toml` 后，在终端运行 `npx wrangler deploy` 来部署 Worker：

```sh
$ npx wrangler deploy
```

部署 Worker 后，你所需的 HTTP header调整将生效。在部署 Worker 的过程中，你可以继续正常查看 Pages 应用程序的内容。