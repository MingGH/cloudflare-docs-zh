---
pcx_content_type: how-to
title: Pelican
---

# Pelican

[Pelican](https://docs.getpelican.com)是一个用 Python 编写的静态网站生成器。有了 Pelican，你可以直接用你选择的 reStructuredText 或 Markdown 格式编辑器编写内容。

## 创建一个Pelican项目

首先，创建 Pelican 项目目录。将 `cd` 存入新目录并运行：

```sh
$ python3 -m pip install pelican
```

然后运行:

```sh
$ pip freeze > requirements.txt
```

在项目中创建一个名为 `content` 的目录：

```sh
$ mkdir content
```

这是你将在构建命令中设置的目录名称。

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，在**设置构建和部署**部分，选择 *Pelican*作为你的**框架预设**。你的选择将提供以下信息。构建命令 `pelican content `指的是本指南前面创建的 `content `文件夹。

{{<pages-build-preset framework="pelican">}}

4. 选择 **环境变量(高级)**，将 `PYTHON_VERSION` 变量设置为 `3.7`。

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。

每次你向 Pelican 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，并在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Pelican">}}