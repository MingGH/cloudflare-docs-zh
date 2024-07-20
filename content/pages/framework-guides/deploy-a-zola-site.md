---
pcx_content_type: how-to
title: Zola
---

# Zola

[Zola](https://www.getzola.org/)是一个快速的静态网站生成器，只需一个二进制文件，内置所有功能。在本指南中，你将创建一个新的 Zola 应用程序，并使用 Cloudflare Pages 进行部署。你将使用 `zola` CLI 创建一个新的 Zola 站点。

## 安装Zola

首先，使用下面针对操作系统的具体说明，[安装](https://www.getzola.org/documentation/getting-started/installation/) `zola` CLI：

### macOS (Homebrew)

如果使用软件包管理器 [Homebrew](https://brew.sh)，请在终端运行 `brew install` 命令安装 Zola：

```sh
$ brew install zola
```

### Windows (Chocolatey)

如果使用软件包管理器 [Chocolatey](https://chocolatey.org/)，请在终端运行 `choco install` 命令来安装 Zola：

```sh
$ choco install zola
```

### Windows (Scoop)

如果使用软件包管理器 [Scoop](https://scoop.sh/)，请在终端运行 `scoop install` 命令安装 Zola：

```sh
$ scoop install zola
```

### Linux (pkg)

你的 Linux 发行版软件包管理器可能包含 Zola。如果是这种情况，你可以使用发行版的软件包管理器直接安装，例如，使用 `pkg` 在终端运行以下命令：

```sh
$ pkg install zola
```

如果你的软件包管理器不包含 Zola，或者你想直接下载发布版本，请参阅下面的 [**手册**](/pages/framework-guides/deploy-a-zola-site/#manual-installation)部分。

### 手动安装

Zola GitHub 代码库包含适用于各种操作系统的 Zola 命令行工具预构建版本，可在 [Releases 页面](https://github.com/getzola/zola/releases) 找到。

有关安装这些版本的更多说明，请参阅 [Zola 安装指南](https://www.getzola.org/documentation/getting-started/installation/)。

## 创建一个新项目

安装 Zola 后，在终端运行 `zola init` 命令，使用默认模板创建一个新项目：

```sh
$ zola init my-zola-project
```

运行 `zola init` 时，系统会提示三个问题：

1.  你网站的 URL 是什么？(https://example.com)：
    这一项可以暂时留空。

2.  要启用 Sass 编译吗？\Y/n]：Y

3.  是否要启用语法高亮？\y/N]: y

4.  你想建立内容搜索索引吗？\y/N]：是

{{<render file="_tutorials-before-you-start.md">}}

{{<render file="/_framework-guides/_create-github-repository_no_init.md">}}

## 使用 Cloudflare Pages部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

{{<pages-build-preset framework="zola">}}

在配置下方，请确保设置**环境变量(高级)**，以指定 `ZOLA_VERSION`。

例如，`ZOLA_VERSION`: `0.17.2`。

配置网站后，你可以开始首次部署。在部署之前，你应该会看到 Cloudflare 页面正在安装 `zola`、项目依赖项并构建网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。

现在，你可以在 `config.toml` 文件中将该子域添加为`base_url`。

例如

```yaml
# The URL the site will be built for
base_url = "https://my-zola-project.pages.dev"
```

每次你向 Zola 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Zola">}}