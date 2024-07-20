---
pcx_content_type: concept
title: Deploy Hooks
---

# 部署钩子

通过部署钩子，你可以使用源代码库中提交之外的事件源触发部署。每个事件源都可以获得自己唯一的 URL，该 URL 将接收 HTTP POST 请求，以启动新的部署。通过这一功能，你可以将 Pages 与新的或现有的工作流程集成。例如，你可以

- 每当无头内容管理系统中的内容发生变化时，自动部署新版本
- 实施完全定制的 CI/CD 管道，仅在所需条件下进行部署
- 安排 CRON 触发器，在固定的时间轴上更新网站

创建部署钩子

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在 `账户主页 `中，选择 `**工作者和页面**`。
3. 在**概览**中，选择你的页面项目。
4. 转到 **Settings**> **Builds & deployments**，选择 **Add deploy hook**开始配置。

![Cloudflare 仪表板上添加部署钩子](/images/pages/platform/deploy-hooks-add.png)

## 所需参数

要配置部署钩子，必须输入两个关键参数：

1.  **部署钩子名称：**部署钩子的唯一标识符(例如，`contentful-site`)。
2.  **要构建的分支：**部署钩子应构建的版本库分支

![在 Cloudflare 面板上选择要构建的部署钩子名称和分支](/images/pages/platform/deploy-hooks-configure.png)

## 使用部署钩子

配置完成后，部署钩子的唯一 URL 就可以使用了。你将看到 URL 和 POST 请求片段可供复制。

![查看部署钩子新生成的唯一 URL](/images/pages/platform/deploy-hooks-details.png)

每次有请求发送到部署钩子，就会触发一次新的构建。查看部署日志中的 **Source**列，以了解部署钩子触发了哪些部署。

![查看部署钩子触发了哪些部署](/images/pages/platform/deploy-hooks-deployment-logs.png)

## 安全考虑因素

部署钩子与你的项目有唯一的链接，使用时不需要额外的身份验证。虽然这样可以实现完全的灵活性，但重要的是，你必须以保护任何专有信息或应用程序机密的相同方式来保护这些 URL。

如果怀疑有人未经授权使用部署钩子，应删除该部署钩子，并在原处生成一个新的部署钩子。

## 将部署钩子与常见的内容管理系统平台集成

每个 CMS 提供商都不尽相同，在与 Pages 的部署钩子集成方面也会提供不同的途径。以下部分包含一些常用 CMS 平台的分步说明。

### Contentful

Contentful 支持通过其 **Webhooks**功能与 Cloudflare 页面集成。在你的 Contentful 项目设置中，转到 **Webhooks**，创建一个新的 Webhook，并在 **URL**字段中粘贴你唯一的部署钩子 URL。你还可以指定 Contentful Webhook 应转发的事件。默认情况下，Contentful 会在所有项目活动中触发页面部署，这可能有点过于频繁。你可以过滤特定事件，如创建、发布等。

![使用 Contentful 配置部署钩子](/images/pages/platform/contentful.png)

### Ghost

你可以通过创建新的**自定义集成**，配置 Ghost 网站以触发页面部署。在 Ghost 网站的设置中，在**集成**页面创建一个新的自定义集成。

创建的每个自定义集成可附加多个 **网络钩子**。选择**添加网络钩子**，并将**网站已更改(重建)**作为**事件**，创建一个新的网络钩子。然后粘贴唯一的部署钩子 URL 作为 **目标 URL**值。创建此 Webhook 后，每当 Ghost 站点发生变化时，你的 Cloudflare Pages 应用程序就会重新部署。

![使用 Ghost 配置部署钩子](/images/pages/platform/ghost.png)

### Sanity

在 Sanity 项目的设置页面，找到 **Webhooks**部分，然后添加部署钩子 URL，如下所示。默认情况下，Webhook 会触发 Sanity 项目中所有数据集的页面部署钩子。你可以使用 **Dataset**字段将通知过滤到单个数据集，如生产数据集：

![使用 Sanity 配置部署钩子](/images/pages/platform/sanity.png)

### WordPress

你可以通过安装免费的 **WP Webhooks**插件来配置 WordPress，以触发页面部署钩子。该插件包含许多触发器，如**新帖子发送数据、帖子更新发送数据**和**删除帖子发送数据**，所有这些触发器都允许你在 WordPress 数据发生变化时触发新的页面部署。在插件设置的侧边栏上选择一个触发器，然后[**添加 Webhook URL**](https://wordpress.org/plugins/wp-webhooks/)，粘贴你唯一的部署钩子 URL。

![使用 WordPress 配置部署钩子](/images/pages/platform/wordpress.png)

### Strapi

在 Strapi 管理面板中，你可以设置和配置网络钩子，以增强你使用 Cloudflare 页面的体验。在 Strapi 管理面板中

1.  导航至**设置**。
2.  选择 **网络钩子**。
3.  选择 **添加新 Webhook**。
4.  在**名称**表单字段中，给新的网络钩子起一个唯一的名称。
5.  在 **URL**表单字段中，粘贴你唯一的 Cloudflare 部署钩子 URL。

在 Strapi 管理面板中，你可以配置 webhook 以根据事件触发。你可以调整这些设置，以便在创建、更新或删除 Strapi 条目或媒体资产时自动创建 Cloudflare Pages 网站的新部署。

确保将 webhook 配置添加到为 Cloudflare 网站提供支持的 [production](https://strapi.io/documentation/developer-docs/latest/setup-deployment-guides/installation.html) Strapi 应用程序中。

![使用 Strapi 配置部署钩子](/images/pages/platform/strapi.png)

### Storyblok

你可以在 Storyblok 中设置和配置部署钩子来触发事件。在你的 Storyblok 空间中，进入**设置**，向下滚动到**Webhooks**。将你的部署钩子粘贴到**故事已发布和未发布**字段，然后选择**保存**。

![Configuring Deploy Hooks with Storyblok](https://user-images.githubusercontent.com/53130544/161367254-ff475f3b-2821-4ee8-a175-8e96e779aa08.png)
