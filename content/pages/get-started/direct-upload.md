---
pcx_content_type: concept
title: 直接上传
meta:
  title: Direct Upload
  description: 将预构建的资产上传到 Pages 并通过 Wrangler CLI 或 Cloudflare 仪表板进行部署。
---

# 直接上传

直接上传使你能够将预构建的资产上传到 Pages 并部署到 Cloudflare 全球网络。本指南将指导你如何使用 Wrangler 或拖放方法上传资产。

## 先决条件

在使用 Direct Upload 部署项目之前，请运行相应的 [build command](/pages/configuration/build-configuration/#framework-presets)来构建项目。

## 上传方法

准备好预置资产后，有两种方法可以开始上传：

* [Wrangler](/pages/get-started/direct-upload/#wrangler-cli).
* [拖放](/pages/get-started/direct-upload/#drag-and-drop)。

{{<Aside type= "note">}}

在 Direct Upload 项目中，可以切换使用 Wrangler 或拖放创建部署。不过，你不能在通过仪表板上的 Git 整合创建的项目上使用 Direct Upload 创建部署。只有通过 Direct Upload 创建的项目才能使用 Direct Upload 进行更新。

{{</Aside>}}

## 支持的文件类型

以下是各直接上传选项支持的文件类型：
* Wrangler：单个资产文件夹。(不支持压缩文件)。
* 拖放：压缩文件或单一资产文件夹。

## Wrangler CLI

### 设置Wrangler

首先，安装 [`npm`](https://docs.npmjs.com/getting-started)。然后 [安装开发者平台 CLI Wrangler](/workers/wrangler/install-and-update/)。

#### 创建你的项目

使用 [`wrangler login`命令](/workers/wrangler/commands/#login)登录Wrangler。然后运行 [`pages project create` 命令](/workers/wrangler/commands/#project-create)：

```sh
$ npx wrangler pages project create
```

然后系统会提示你指定项目名称。项目的服务地址为 `<PROJECT_NAME>.pages.dev`(如果项目名称已被占用，则项目名称加上几个随机字符)。系统还会提示你指定生产分支。

后续部署将重复使用这两个值(保存在 `node_modules/.cache/wrangler` 文件夹中)。

#### 部署你的资产

从这里开始，你就创建了一个空项目，现在就可以在生产环境中为首次部署和所有后续部署部署资产了。为此，请运行 [`wrangler pages deploy`](/workers/wrangler/commands/#deploy-1) 命令：

```sh
$ npx wrangler pages deploy <BUILD_OUTPUT_DIRECTORY>
```

在[框架预设下的构建目录](/pages/configuration/build-configuration/#framework-presets)中为项目找到合适的构建输出目录。

你的生产部署将位于 `<PROJECT_NAME>.pages.dev`。

{{<Aside type= "note">}}

在使用 `wrangler pages deploy` 命令之前，你需要确保自己在项目中。如果不在，也可以输入项目路径。

{{</Aside>}}

要将资产部署到预览环境，请运行

```sh
$ npx wrangler pages deploy <OUTPUT_DIRECTORY> --branch=<BRANCH_NAME>
```

你创建的每个分支都会有一个分支别名，地址为 `<BRANCH_NAME>.<PROJECT_NAME>.pages.dev`。

{{<Aside type= "note">}}

如果你在 Git 工作区中，Wrangler 会自动为你提取分支信息。否则，你需要在此命令中指定你的分支。

{{</Aside>}}

如果想简化项目创建和资产部署步骤，也可以使用部署命令同时创建和部署资产。如果先执行该命令，系统仍会提示你指定项目名称和生产分支。如上所述，这些值仍将被缓存，以备后续部署之用。如果缓存已经存在，而你想创建一个新项目，则需要运行 [`create`命令](#create-your-project)。

#### 其他有用的命令

如果想使用 Wrangler 获取所有可用于直接上传的项目列表，请使用 [`pages project list`](/workers/wrangler/commands/#project-list)：

```sh
$ npx wrangler pages project list
```

如果想使用 Wrangler 获取特定项目的所有唯一预览 URL 的列表，请使用 [`pages deployment list`](/workers/wrangler/commands/#deployment-list)：

```sh
$ npx wrangler pages deployment list
```

有关如何将 Wrangler 与 GitHub Actions、Circle CI 和 Travis CI 等持续集成工具一起用于持续部署的逐步说明，请参阅 [Use Direct Upload with continuous integration](/pages/how-to/use-direct-upload-with-continuous-integration/)。

## 拖放

#### 使用拖放功能部署项目

通过拖放进行部署：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/login)。
2. 在**账户主页**中，选择账户 > **员工和页面**。
3. 选择 **创建应用程序**> **页面**> **上传资产**。
4. 在提供的字段中输入项目名称，然后拖放资产。
5. 选择 **部署**。

你的项目将从`<PROJECT_NAME>.pages.dev `提供。然后将构建输出目录拖放到上传框中。文件上传成功后，选择**保存并部署**，然后继续新部署的项目。

#### 创建新部署

创建项目后，选择**创建新部署**，开始网站的新版本。接下来，选择新部署是部署到生产环境还是预览环境。如果选择预览，则可以创建新的部署分支或输入现有分支。

## 故障排除

#### 限制

| 上传方法 | 文件限制 | 文件大小|
| ------------- | ------------ | -------- |
| Wrangler      | 20,000 files | 25 MiB   |
| Drag and drop | 1,000 files  | 25 MiB   |

如果使用拖放方法，如果资产太大，上传不成功，旁边会出现红色警告符号。在这种情况下，你可以选择删除该资产，但不能替换它。为此，你必须重新上传整个项目。

### 生产分支配置

{{<render file="_prod-branch-update.md" productFolder="/pages/">}}

### 功能

从 Cloudflare 控制面板进行的拖放部署目前不支持编译 [Pages Functions](/pages/functions/) 的 `functions `文件夹。要部署 `functions` 文件夹，必须使用 Wrangler。使用 Wrangler 部署项目时，如果运行命令的地方存在 `functions` 文件夹，则该 `functions` 文件夹将与项目一起上传。

不过，请注意 Wrangler 和从仪表板拖放部署都支持 `_worker.js`文件。

