---
updated: 2022-07-27
pcx_content_type: how-to
title: Set build commands per branch
---

# 为每个分支设置构建命令

本指南将指导你如何在特定分支上设置编译命令。你将使用 `CF_PAGES_BRANCH` 环境变量在指定分支而非生产分支上运行脚本。本指南假定你拥有 Cloudflare 账户和 Pages 项目。

## 设置

在项目目录下创建一个 `.sh` 文件。你可以选择文件名，但我们建议你将文件命名为 `build.sh`。

在下面的脚本中，你将使用 `CF_PAGES_BRANCH` 环境变量来检查当前正在构建的分支。用以下内容填充你的 `.sh` 文件：

```bash
# !/bin/bash

if [ "$CF_PAGES_BRANCH" == "production" ]; then
  # Run the "production" script in `package.json` on the "production" branch
  # "production" should be replaced with the name of your Production branch

  npm run production

elif [ "$CF_PAGES_BRANCH" == "staging" ]; then
  # Run the "staging" script in `package.json` on the "staging" branch
  # "staging" should be replaced with the name of your specific branch

  npm run staging

else
  # Else run the dev script
  npm run dev
fi
```

## Publish your changes

将更改生效：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 在 "账户主页 "中，选择 "**Workers & Pages**" > 在 "**Overview**"中，选择 "Pages "项目。
3. 转到 **设置**> **构建和部署**> **构建配置**> **编辑配置**。
3. 将 **Build command**字段值更新为 `bash build.sh`，然后选择 **Save**。

要测试构建是否成功，请部署项目。