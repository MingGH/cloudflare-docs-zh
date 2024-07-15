---
pcx_content_type: concept
title: Known issues
weight: 4
---

# 已知问题

以下是 Cloudflare 页面的一些已知错误和问题：

## 建立和部署

- GitHub 和 GitLab 是目前唯一支持自动 CI/CD 构建的平台。[直接上传](/pages/get-started/direct-upload/) 允许你集成自己的构建平台或从本地电脑上传。

- Cloudflare 页面目前不支持增量构建。

- 通过仪表板的 "直接上传 "选项上传`/functions`目录不起作用(请参阅[在直接上传中使用功能](/pages/get-started/direct-upload/#functions))。

- 来自分叉版本库的提交/预览不会创建预览。未来将提供这方面的支持。

## Git 配置

- 为 Pages 应用程序选择 GitHub/GitLab 仓库后，该仓库将无法更改。如果需要更新，请删除 Pages 项目并创建一个指向不同版本库的新项目。

## 构建配置

- `*.pages.dev` 子域目前不能更改。如果你需要更改`*.pages.dev`子域，请删除你的项目并创建一个新项目。
- Hugo 会自动运行旧版本。要运行最新版本的 Hugo(例如 `0.101.0`)，需要设置一个环境变量。将 `HUGO_VERSION` 设为 `0.101.0` 或你选择的 Hugo 版本。

- 默认情况下，Cloudflare 在页面构建环境中使用 Node `12.18.0`。如果你需要使用更新的 Node 版本，请参阅 [构建配置页面](/pages/configuration/build-configuration/) 了解配置选项。

- 对于从 Netlify 迁移的用户，Cloudflare 不支持 Netlify 的表单功能。[页面函数](/pages/functions/) 与 Netlify 的无服务器函数相当。

## 自定义域

- 目前无法使用

  - 通配符，例如 `*.domain.com`。
  - 已在该域上路由的工人。

- 目前无法添加已启用 Cloudflare 访问策略的自定义域。

- Cloudflare 的负载平衡器不适用于`*.pages.dev`项目；会出现`Error 1000: DNS points to prohibited IP`。

- 添加自定义域时，如果 Cloudflare 无法验证该主机名上的 SSL 证书请求，该域将无法验证。为了验证 SSL，请确保 Cloudflare Access 或 Cloudflare Worker 允许请求进入验证路径：`http://{domain_name}/.well-known/acme-challenge/*`。

- 由于 Cloudflare for SaaS 的 [证书优先级](/ssl/reference/certificate-and-hostname-priority/)，[高级证书](/ssl/edge-certificates/advanced-certificate-manager/) 不能与 Cloudflare 页面一起使用。

## Pages功能

- [函数](/pages/functions/)目前不支持添加/删除多填充，因此你的捆绑程序(例如 webpack)可能无法运行。

- `passThroughOnException()`目前不适用于高级模式页面函数(使用 `_worker.js` 文件的页面函数)。

- 目前，`passThroughOnException()`不像 Workers 中那样有弹性。我们目前将页面函数代码封装在一个 `try`/`catch` 块中，并回退到调用 `env.ASSETS.fetch()`。这意味着任何关键故障(如超过 CPU 时间或超过内存)仍可能引发错误。

## 启用`*.pages.dev`域的访问权限

如果你想为你的预览部署和`*.pages.dev`域启用 [Cloudflare Access](https://www.cloudflare.com/teams-access/)]，你必须这样做：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/login)。
2. 从 "账户主页"，选择 "**工作者和页面**"。
3. 在**概览**中，选择你的页面项目。
4. 转到 **设置**> **启用访问策略**。
5. 在为预览部署创建的访问策略上选择 **编辑**。
6. 在 "编辑 "中，转到 "**概览**"。
7. 在**子域**字段中，删除通配符 (`*`) 并选择**保存应用程序**。在此步骤中，你可能需要更改 **应用程序名称**，以避免出错。

在此步骤中，你的 `*.pages.dev` 域已在 Access 后面被加密。重新保护预览部署：

8. 返回页面项目 > **设置**> **常规**> 并重新选择 **启用访问策略**。
9. 检查是否已创建两个访问策略，一个用于`*.pages.dev`域，另一个用于预览部署 (`*.<YOUR_SITE>.pages.dev`)。

如果你有一个自定义域，并在 Access 后面保护了你的 `*.pages.dev` 域，则必须这样做：

10. 在 [Cloudflare Zero Trust](https://one.dash.cloudflare.com/) 中选择 **Add an application**> **Self hosted**。
11. 输入**应用程序名称**，并从_域_下拉菜单中选择自定义域。
12. 选择 "下一步 "**，然后配置访问规则，以定义谁可以访问身份验证页面。
13. 选择 **添加应用程序**。

{{<Aside type="warning">}}

如果你没有为自定义域配置访问策略，则访问身份验证会呈现，但对自定义域访问者不起作用。如果你的 Pages 项目有自定义域，请确保按照上述步骤 10 至 13 中的说明添加访问策略，以避免出现任何身份验证问题。

{{</Aside>}}

如果你有未列出的问题，请在 Cloudflare Workers Discord 中告知团队。请登录 [discord.cloudflare.com](https://discord.cloudflare.com) 获取邀请，并在 #pages-general 频道分享你的错误报告。

## 删除部署次数较多的项目

如果你的页面项目部署数量较多(超过 800 个)，你可能无法删除该项目。Cloudflare 团队正在跟踪此问题。

作为一种变通方法，请查看以下步骤以删除 Pages 项目中的所有部署。删除部署后，就可以删除 Pages 项目了。

1. 通过以下链接下载 `delete-all-deployments.zip` 文件：https://pub-505c82ba1c844ba788b97b1ed9415e75.r2.dev/delete-all-deployments.zip。
2. 解压缩 `delete-all-deployments.zip` 文件。
3. 打开终端，`cd`进入`delete-all-deployments`目录。
4. 在 `delete-all-deployments` 目录中，运行 `npm install` 安装依赖项。
5. 查看以下命令，决定要进行的删除操作：

* 要删除除实时生产部署外的所有部署(不包括 [aliased deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/#preview-aliases))：

```sh
$ CF_API_TOKEN=<YOUR_CF_API_TOKEN> CF_ACCOUNT_ID=<ACCOUNT_ID> CF_PAGES_PROJECT_NAME=<PROJECT_NAME> npm start
```

* 删除除实时生产部署以外的所有部署(包括 [aliased deployments](https://developers.cloudflare.com/pages/configuration/preview-deployments/#preview-aliases)，例如 `staging.example.pages.dev`)：

```sh
$ CF_API_TOKEN=<YOUR_CF_API_TOKEN> CF_ACCOUNT_ID=<ACCOUNT_ID> CF_PAGES_PROJECT_NAME=<PROJECT_NAME> CF_DELETE_ALIASED_DEPLOYMENTS=true npm start
```

要查找你的 Cloudflare API 令牌，请登录 [Cloudflare 仪表板](https://dash.cloudflare.com)，选择屏幕右上方的用户图标 > 转到 **我的资料**> **API令牌**。

要查找帐户 ID，请参阅 [查找区域和帐户 ID](/fundamentals/setup/find-account-and-zone-ids/)。

## 在 Cloudflare 负载平衡器中使用页面作为Origin

如果未设置主机头，[Cloudflare 负载平衡](/load-balancing/) 将无法工作。要使用 Pages 项目作为目标，请确保在 [创建池] (/load-balancing/pools/create-pool/#create-a-pool) 时选择 **添加主机标头**，并将主机标头值和端点地址都设置为你的 `pages.dev` 域。

有关完整教程，请参阅 [使用 Cloudflare 页面作为Origin](/load-balancing/pools/cloudflare-pages-origin/)。