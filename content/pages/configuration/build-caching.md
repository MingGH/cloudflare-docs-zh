---
pcx_content_type: concept
title: Build caching
---

# 建立缓存(测试版)

通过开启构建缓存，在两次构建之间恢复依赖关系和构建输出，从而缩短 Pages 的构建时间。在 Pages 项目上启用构建缓存后进行的首次构建将保存到缓存中。除非另有配置，否则之后的每次构建都将从缓存中还原。

## 要求

构建缓存需要 [V2 构建系统](/pages/configuration/language-support-and-tools/) 或更高版本。要从 V1 升级，请参阅 [V2 构建系统迁移说明](/pages/configuration/language-support-and-tools/#v2-build-system)。

## 配置

### 启用构建缓存

在 Cloudflare 仪表板中启用构建缓存：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在 "账户主页 "中，选择 "**工作者和页面**"。
3. 在**概览**中，选择你的页面项目。
4. 转到 **设置**> **构建和部署**> **构建缓存**，然后选择 **启用构建缓存**。

### 清除缓存

如有需要，例如在调试构建问题时，可清除项目的构建缓存。清除编译缓存

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在 "账户主页 "中，选择 "**工作者和页面**"。
3. 在**概览**中，选择你的页面项目。
4. 转到 **设置**> **构建和部署**> **构建缓存**。
5. 选择 **清除缓存**，清除构建缓存。

## 构建缓存的工作原理

启用后，构建缓存将自动检测并缓存每次构建的数据。请参阅 [框架](/pages/configuration/build-caching/#frameworks)，查看哪些目录会从构建缓存中自动保存和恢复。

### 软件包管理器

软件包管理器缓存会自动保存到构建缓存中，以加快依赖关系的安装。页面将缓存以下软件包管理器的全局缓存目录：

- [yarn 1](https://yarnpkg.com/)
- [npm](https://www.npmjs.com/)
- [pnpm](https://pnpm.io/)
- [bun](https://bun.sh/)

### 框架

缓存框架的构建输出可加快后续构建时间。构建缓存支持以下框架：

| 框架 | 缓存的目录|
| ---------- | --------------------------------------------- |
| Astro      | `node_modules/.astro`                         |
| Docusaurus | `node_modules/.cache`, `.docusaurus`, `build` |
| Eleventy   | `.cache`                                      |
| Gatsby     | `.cache`, `public`                            |
| Next.js    | `.next/cache`                                 |
| Nuxt       | `node_modules/.cache/nuxt`                    |

## 限制

在测试期间，有以下限制：

- **保留**：缓存在最后一次读取日期后七天被清除。未读缓存工件将在创建七天后清除。
- **存储**：每个项目分配 10 GB。如果项目缓存超过此限制，项目将自动开始删除最近读取次数最少的工件。

## 反馈

如果你希望看到软件包管理器或框架获得支持，请通过 [Cloudflare Developer Discord](https://discord.com/invite/cloudflaredev) 的页面频道告诉我们。