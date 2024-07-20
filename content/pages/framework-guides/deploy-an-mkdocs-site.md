---
pcx_content_type: how-to
title: MkDocs
---

# MkDocs

[MkDocs](https://www.mkdocs.org/)是一个现代文档平台，团队可在此记录产品、内部知识库和应用程序接口。

## 安装 MkDocs

MkDocs 要求系统安装最新版本的 Python 和 Python 包管理器 pip。要安装 pip，请参阅 [MkDocs 安装指南](https://www.mkdocs.org/user-guide/installation/)。安装好 pip 后，运行

```sh
$ pip install mkdocs
```

## 创建一个 MkDocs 项目

使用 `mkdocs new` 命令创建新应用程序：

```sh
$ mkdocs new <PROJECT_NAME>
```

然后`cd`进入你的项目，将 MkDocs 及其依赖项放入`requirements.txt`文件：

```sh
$ pip freeze > requirements.txt
```

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository.md">}}

你已成功创建了一个 GitHub 仓库，并将 MkDocs 项目推送到该仓库。

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择你创建的新 GitHub 仓库，在**设置构建和部署**部分，选择 *MkDocs*作为你的**框架预设**。你的选择将提供以下信息：

{{<pages-build-preset framework="mkdocs">}}

4. 转到 **环境变量(高级)**> **添加变量**> 添加变量 `PYTHON_VERSION` 并设置值为 `3.7`。

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。

每次你向 MkDocs 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，并在部署到生产环境之前预览网站更改的外观。

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{<render file="/_framework-guides/_learn-more.md" withParameters="MkDocs">}}