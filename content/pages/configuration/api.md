---
pcx_content_type: concept
title: REST API
---

# REST API

[Pages API](/api/operations/pages-project-get-projects)使你能够构建自动化，并将 Pages 与你的开发工作流集成。在高层次上，API 接口可让你管理部署和构建并配置项目。Cloudflare 支持用于headless CMS 部署的 [Deploy Hooks](/pages/configuration/deploy-hooks/)。请参阅 [API 文档](https://api.cloudflare.com/) 了解对象类型和接口的完整分类。

## 如何使用应用程序接口

### 获取应用程序接口令牌

创建 API 令牌

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com)。
2. 选择仪表板 > **我的个人资料**右上方的用户图标。
3. 选择 [**API 令牌**](https://dash.cloudflare.com/profile/api-tokens) > **创建令牌**。
4. 你可以转到 **编辑 Cloudflare Workers**模板 > **使用模板**或转到 **创建自定义令牌**> **开始**。如果创建自定义令牌，则需要确保添加**Cloudflare 页面**权限和**编辑**访问权限。

### 请求

创建令牌后，你就可以使用请求headers中的 API 令牌对 API 进行身份验证和发出请求。例如，下面是一个获取项目中所有部署的 API 请求。

```bash
curl 'https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments' \
--header 'Authorization: Bearer <API_TOKEN>'
```

替换 `{account_id}`、`{project_name}` 和 `<API_TOKEN>`，使用你的一个项目进行尝试。更多信息请参阅 [查找账户 ID](/fundamentals/setup/find-account-and-zone-ids/)。

## 示例

当 API 与 Cloudflare Workers(在 Cloudflare 全球网络上部署无服务器功能的最简单方法)结合使用时，功能会更加强大。以下部分包括如何使用页面 API 的三个代码示例。要构建和部署这些示例，请参阅 [入门指南](/workers/get-started/guide/)。

#### 每小时触发一次新的构建

假设我们有一个从实时源提取数据来编译静态输出的内容管理系统。你可以通过使用 API 定期触发新的编译，尽可能保持静态内容的最新状态。

```js
const endpoint = "https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments";

export default {
  async scheduled(_, env) {
    const init = {
      method: "POST",
      headers: {
        "Content-Type": "application/json;charset=UTF-8",
        // We recommend you store the API token as a secret using the Workers dashboard or using Wrangler as documented here: https://developers.cloudflare.com/workers/wrangler/commands/#secret
        "Authorization": `Bearer ${env.API_TOKEN}`,
      },
    };

    await fetch(endpoint, init);
  }
}
```

部署 JavaScript Worker 后，在 Worker 中设置一个 cron 触发器来定期运行此脚本。详情请参阅 [Cron Triggers](/workers/configuration/cron-triggers/)。

### 一周后删除旧部署

Cloudflare Pages 在预览链接上托管并提供所有项目部署。假设你想保持项目的私密性，防止他人访问你的旧部署。你可以使用 API 在一个月后删除部署，使其不再在线公开。分支的最新部署无法删除。

```js
const endpoint = "https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments";
const expirationDays = 7;

export default {
  async scheduled(_, env) {
    const init = {
      headers: {
        "Content-Type": "application/json;charset=UTF-8",
        // We recommend you store the API token as a secret using the Workers dashboard or using Wrangler as documented here: https://developers.cloudflare.com/workers/wrangler/commands/#secret
        "Authorization": `Bearer ${env.API_TOKEN}`,
      },
    };

    const response = await fetch(endpoint, init);
    const deployments = await response.json();

    for (const deployment of deployments.result) {
      // Check if the deployment was created within the last x days (as defined by `expirationDays` above)
      if ((Date.now() - new Date(deployment.created_on)) / 86400000 > expirationDays) {
        // Delete the deployment
        await fetch(`${endpoint}/${deployment.id}`, {
          method: "DELETE",
          headers: {
            "Content-Type": "application/json;charset=UTF-8",
            "Authorization": `Bearer ${env.API_TOKEN}`,
          },
        });
      }
    }
  }
}
```

部署 JavaScript Worker 后，可以在 Worker 中设置一个 cron 触发器来定期运行该脚本。详情请参考 [Cron Triggers guide](/workers/configuration/cron-triggers/)。

### 共享项目信息

想象一下，你在一个使用 Pages 构建网站的开发团队中工作。你希望有一种简单的方式来共享部署预览链接和构建状态，而无需共享 Cloudflare 账户。使用 API，你可以轻松共享项目信息，包括部署状态和预览链接，并从 Cloudflare Worker 以 HTML 的形式提供这些内容。

```js
const deploymentsEndpoint =
  "https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}/deployments";
const projectEndpoint =
  "https://api.cloudflare.com/client/v4/accounts/{account_id}/pages/projects/{project_name}";

export default {
  async fetch(request, env) {
    const init = {
      headers: {
        "content-type": "application/json;charset=UTF-8",
        // We recommend you store the API token as a secret using the Workers dashboard or using Wrangler as documented here: https://developers.cloudflare.com/workers/wrangler/commands/#secret
        "Authorization": `Bearer ${env.API_TOKEN}`,
      },
    };

    const style = `body { padding: 6em; font-family: sans-serif; } h1 { color: #f6821f }`;
    let content = "<h2>Project</h2>";

    let response = await fetch(projectEndpoint, init);
    const projectResponse = await response.json();
    content += `<p>Project Name: ${projectResponse.result.name}</p>`;
    content += `<p>Project ID: ${projectResponse.result.id}</p>`;
    content += `<p>Pages Subdomain: ${projectResponse.result.subdomain}</p>`;
    content += `<p>Domains: ${projectResponse.result.domains}</p>`;
    content += `<a href="${projectResponse.result.canonical_deployment.url}"><p>Latest preview: ${projectResponse.result.canonical_deployment.url}</p></a>`;

    content += `<h2>Deployments</h2>`;
    response = await fetch(deploymentsEndpoint, init);
    const deploymentsResponse = await response.json();

    for (const deployment of deploymentsResponse.result) {
      content += `<a href="${deployment.url}"><p>Deployment: ${deployment.id}</p></a>`;
    }

    let html = `
      <!DOCTYPE html>
      <head>
        <title>Example Pages Project</title>
      </head>
      <body>
        <style>${style}</style>
        <div id="container">
          ${content}
        </div>
      </body>`;

    return new Response(html, {
      headers: {
        "Content-Type": "text/html;charset=UTF-8",
      },
    });
  }
}
```

## 相关资源

- [页面 API 文档](/api/operations/pages-project-get-projects)
- [Workers入门指南](/workers/get-started/guide/)
- [Workers的 Cron 触发器](/workers/configuration/cron-triggers/)