---
updated: 2020-09-28
difficulty: Beginner
pcx_content_type: tutorial
title: 从 Firebase 迁移
---

# 从 Firebase 迁移

在本教程中，你将学习如何将现有 Firebase 应用程序迁移到 Cloudflare Pages。你应该已经在 Firebase 上部署了一个现有项目，并希望将其托管到 Cloudflare Pages 上。

## 查找构建命令和构建目录

要将应用程序移至 Cloudflare Pages，你需要找到构建命令和构建目录。

你将使用这些文件告诉 Cloudflare Pages 如何部署你的项目。如果你使用 `firebase` 命令行工具从本地计算机手动部署，则 `firebase.json` 配置文件应包含一个 `public` 密钥，该密钥将作为你的构建目录：

```json
---
header: firebase.json
---
{
  "public": "public"
}
```

Firebase Hosting 不会询问你的构建命令，因此如果你运行的是标准 JavaScript 设置，你可能会使用 `npm build` 或与你使用的框架或工具相关的特定命令(例如 `ng build`)。

找到构建目录和构建命令后，就可以将项目移动到 Cloudflare 页面。

## 创建一个新的 Pages 项目

如果尚未将静态网站推送到 GitHub，则应在继续之前推送。这样还能使用自动部署和 [部署预览](/pages/configuration/preview-deployments/) 等功能。

你可以访问 [repo.new](https://repo.new)，创建一个新仓库，然后按照说明将项目推送到 GitHub。

使用 [入门指南](/pages/get-started/) 将你的项目添加到 Cloudflare Pages，使用你之前保存的 **build 命令**和 **build 目录**。

## 清理旧应用程序并分配域名

部署应用程序后，转到 Firebase 面板并删除旧的 Firebase 项目。在 Cloudflare DNS 域名设置中，确保将域名的 CNAME 记录从 Firebase 更新到 Cloudflare Pages。

完成本指南后，你已成功将 Firebase 项目迁移到 Cloudflare Pages。