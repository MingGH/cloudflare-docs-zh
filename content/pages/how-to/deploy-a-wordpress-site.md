---
updated: 2023-04-01
difficulty: Intermediate
content_type: 📝 Tutorial
pcx_content_type: tutorial
title: Deploy a static WordPress site
---

# 部署一个 WordPress 静态网站

## 概览

在本指南中，你将使用 WordPress 插件 [Simply Static](https://wordpress.org/plugins/simply-static/)，将现有 WordPress 网站转换为使用 Cloudflare Pages 部署的静态网站。

## 先决条件

本指南假定你是

- WordPress 网站的管理员账户。
- 能够在网站上安装 WordPress 插件。

## 设置

首先，安装 [Simply Static](https://wordpress.org/plugins/simply-static/) 插件，导出你的 WordPress 网站。在 WordPress 面板中，转到 **Plugins** > **Add New**。

搜索 `Simply Static`，并确认要安装的插件与下面的插件一致。

![Simply Static 插件](/images/pages/how-to/simply-static.png)

在插件上选择 **安装**。安装完成后，选择**激活**。

### 导出你的 WordPress 网站

安装插件后，进入 WordPress 面板 > **Simply Static** > **GENERATE STATIC FILES**。

在**活动日志**中，找到**已创建的 ZIP 压缩包**信息，然后选择**单击此处下载**下载你的 ZIP 文件。

### 使用页面部署 WordPress 网站

下载 ZIP 文件后，将网站部署到 Pages：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在账户主页，选择 **Workers & Pages** > **Create application** > **Pages** > **上载资产**。
3. 为项目命名 > **创建项目**。
4. 拖放 ZIP 文件（或解压缩的资产文件夹），或从电脑中选择该文件。
5. 上传文件后，选择 **部署站点**。

你的 WordPress 网站现在将在 Pages 上运行。

每次对 WordPress 网站进行更改时，你都需要从 WordPress 控制面板下载一个新的 ZIP 文件，然后重新部署到 Cloudflare 页面。Simply Static 免费版不提供自动更新功能。

## 限制

WordPress 网站的某些功能在静态网站环境中无法使用：

- WordPress 表单。
- WordPress 评论。
- 任何指向 `/wp-admin`或类似 WordPress 内部路径的链接。

## 总结

按照本指南，你已成功将 WordPress 网站的静态版本部署到 Cloudflare 页面。

通过提供静态版本的网站，你可以

- 将 WordPress 网站移动到自定义域或子域。请参阅 [自定义域](/pages/configuration/custom-domains/) 了解更多信息。
- 在本地运行 WordPress 实例，或将 WordPress 网站置于[Cloudflare 访问](/pages/configuration/preview-deployments/#customize-preview-deployments-access)之后，只允许你的贡献者访问。这对 WordPress 网站及其内容的攻击向量数量有很大影响。
- 将 WordPress 托管计划降级为更便宜的计划。由于 WordPress 实例现在所需的内存和带宽较小，你通常可以将其托管在更便宜的计划上，或转到共享托管。

与[Cloudflare 开发人员 Discord 社区](https://discord.cloudflare.com) 联系，提出问题并与其他开发人员讨论平台。