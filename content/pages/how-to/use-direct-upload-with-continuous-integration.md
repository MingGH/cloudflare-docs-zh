---
pcx_content_type: how-to
title: 使用直接上传和持续集成
---

# 使用直接上传和持续集成

Cloudflare Pages 支持直接上传预构建资产，允许你为应用程序使用自定义构建步骤，并使用 [Wrangler](/workers/wrangler/install-and-update/) 部署到 Pages。本指南将教你如何使用持续集成将应用程序部署到 Pages。

## 使用 Wrangler 进行部署

在项目目录下安装 [Wrangler](/workers/wrangler/install-and-update/)，这样就可以通过运行以下命令来部署预构建资产文件夹：

```sh
# Publish created project
$ CLOUDFLARE_ACCOUNT_ID=<ACCOUNT_ID> npx wrangler pages deploy <DIRECTORY> --project-name=<PROJECT_NAME>
```

## 从 Cloudflare 获取证书

### 生成 API 令牌

生成 API 令牌：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/profile/api-tokens)。
2. 从仪表板右上角用户图标的下拉菜单中选择 **我的个人资料**。
3. 选择 **API 令牌**> **创建令牌**。
4. 在**自定义令牌**下，选择**开始**。
5. 在**令牌名称**字段中为 API 令牌命名。
6. 在 **Permissions**下，选择 _Account_、_Cloudflare Pages_ 和 _Edit_：
7. 选择 **继续到摘要**> **创建令牌**。

![按照上述说明为 Cloudflare 页面创建 API 令牌](/images/pages/how-to/select-api-token-for-pages.png)

现在你已经创建了 API 令牌，可以使用它从持续集成平台推送你的项目。

### 获取项目账户 ID

要找到你的帐户 ID，请登录 Cloudflare 面板 > 在 **Account Home**中选择你的区域 > 在右侧菜单 **API**下的 **Overview**中找到你的帐户 ID。如果尚未添加区域，请选择**添加站点**添加区域。你可以从 [Cloudflare 的注册商](/registrar/) 处购买域名。

## 使用 GitHub 操作

[GitHub Actions](https://docs.github.com/en/actions)是一个持续集成和持续交付(CI/CD)平台，可让你在使用 GitHub 时自动执行构建、测试和部署管道。你可以创建工作流来构建和测试仓库中的每个拉取请求，或将合并后的拉取请求部署到生产中。

完成项目设置后，你可以设置一个 GitHub 操作，以便使用 Wrangler 自动执行后续部署。

### 将 Cloudflare 认证添加到 GitHub 秘密中

在你设置的 GitHub 操作中，需要使用环境变量将项目推送到 Cloudflare Pages。要在项目的 GitHub 仓库中添加这些环境变量的值，请执行以下操作

1.  访问 GitHub 上的项目仓库。
2.  在版本库名称下，选择 **设置**。
3.  选择 **秘密**> **操作**> **新存储库秘密**。
4.  创建一个秘密，将 **CLOUDFLARE_ACCOUNT_ID**作为名称，值为你的 Cloudflare 帐户 ID。
5.  创建另一个秘密，将 **CLOUDFLARE_API_TOKEN**作为名称，值为你的 Cloudflare API 令牌。

将 Cloudflare 帐户 ID 和 Cloudflare API 令牌的值分别添加为 `CLOUDFLARE_ACCOUNT_ID` 和 `CLOUDFLARE_API_TOKEN`。这将确保这些机密是安全的，每次运行 Action 时都会访问这些机密。

#### 设置工作流程

在项目根目录下创建一个 `.github/workflows/pages-deployment.yaml` 文件。该`.github/workflows/pages-deployment.yaml`文件将包含你在请求中指定的作业，即`on: [push]`。也可以是拉取请求。有关 GitHub Actions 语法的详细解释，请参阅[官方文档](https://docs.github.com/en/actions)。

在 `pages-deployment.yaml` 文件中，复制以下内容：

```yaml
---
filename: .github/workflows/pages-deployment.yaml
---
on: [push]
jobs:
  deploy:
    runs-on: ubuntu-latest
    permissions:
      contents: read
      deployments: write
    name: Deploy to Cloudflare Pages
    steps:
      - name: Checkout
        uses: actions/checkout@v3
      # Run your project's build step
      # - name: Build
      #   run: npm install && npm run build
      - name: Publish
        uses: cloudflare/pages-action@v1
        with:
          apiToken: ${{ secrets.CLOUDFLARE_API_TOKEN }}
          accountId: ${{ secrets.CLOUDFLARE_ACCOUNT_ID }}
          projectName: YOUR_PROJECT_NAME # e.g. 'my-project'
          directory: YOUR_DIRECTORY_OF_STATIC_ASSETS # e.g. 'dist'
          gitHubToken: ${{ secrets.GITHUB_TOKEN }}
```

在上述代码块中，你设置了一个 Action，当你将代码推送到版本库时运行该 Action。将 `YOUR_PROJECT_NAME` 替换为 Cloudflare Pages 项目名称，将 `YOUR_DIRECTORY_OF_STATIC_ASSETS` 替换为项目的输出目录。

GitHub Actions 将自动提供 `${{ secrets.GITHUB_TOKEN }}` 和 `contents: read` 和 `deployments: write` 权限。这将使我们的 Cloudflare Pages 操作能够代表你创建部署。

{{<Aside type="note">}}

除非在 `with` 部分添加了 `branch` 选项，否则此工作流程会自动在当前的 git 分支上触发。

{{</Aside>}}

## 使用 CircleCI 进行 CI/CD

[CircleCI](https://circleci.com/)是另一个持续集成和持续交付(CI/CD)平台，可以让你的构建、测试和部署管道自动化。它可以通过缓存、docker 层缓存和资源类进行配置，以高效运行复杂的管道。

与 GitHub Actions 类似，CircleCI 可以使用 Wrangler 在每次推送代码时持续部署项目。

### 将 Cloudflare 证书添加到 CircleCI

生成 Cloudflare API 标记并在仪表板中找到账户 ID 后，你需要将其添加到 CircleCI 仪表板，以便在项目中使用环境变量。

在 CircleCI 网络应用程序中添加环境变量：

1. 进入页面项目 > **设置**。
2. 在侧菜单中选择 **项目**。
3. 选择项目行中的省略号(...)按钮。你将看到添加环境变量的选项。
4. 选择 **环境变量**> **添加环境变量**。
5. 输入新环境变量的名称和值，即 Cloudflare 凭据(`CLOUDFLARE_ACCOUNT_ID` 和`CLOUDFLARE_API_TOKEN`)。

![按照上述说明在 CircleCI 设置中添加环境变量](/images/pages/how-to/project-settings-env-var-v2.png)

#### 设置工作流程

在项目根目录下创建一个 `.circleci/config.yml` 文件。该文件包含根据工作流程顺序执行的作业。在 `config.yml` 文件中，复制以下内容：

```yaml
---
filename: .circleci/config.yml
---
version: 2.1
jobs:
 Publish-to-Pages:
   docker:
     - image: cimg/node:18.7.0

   steps:
     - checkout
     # Run your project's build step
     - run: npm install && npm run build
     # Publish with wrangler
     - run: npx wrangler pages deploy dist --project-name=<PROJECT NAME> # Replace dist with the name of your build folder and input your project name

workflows:
 Publish-to-Pages-workflow:
   jobs:
     - Publish-to-Pages
```

使用 CircleCI 时，你的持续集成工作流程会被分解成多个作业。从上面的代码块中，你可以看到你首先定义了在每次提交时运行的工作列表。例如，你的版本库将运行在预构建的 docker 镜像 `cimg/node:18.7.0`上。它首先用映像中指定的 Node 版本来检查版本库。

{{<Aside type="note" header="Note">}}

Wrangler 要求 Node 版本至少为 `16.17.0`。如果你的 Node.js 版本低于 `16.17.0`，则必须升级。

{{</Aside>}}

你可以使用任何 [`wrangler页面部署`选项](/workers/wrangler/commands/#deploy-1) 来修改 Wrangler 命令。

完成所有指定步骤后，在文件末尾定义 "工作流"。有关使用 CircleCI 创建自定义流程的更多信息，请参阅 [官方文档](https://circleci.com/docs/2.0/concepts/)。

## CI/CD 的 Travis CI

Travis CI 是一款开源持续集成工具，可处理项目工作流程中的特定任务，如拉取请求和代码推送。Travis CI 可以集成到 GitHub 项目、数据库和其他在构建配置中启用的预装服务中。要使用 Travis CI，你必须拥有一个 GitHub、Bitbucket、GitLab 或 Assembla 账户。

### 将 Cloudflare 证书添加到 TravisCI

在你的 Travis 项目中，添加从 Cloudflare 面板生成的 Cloudflare 凭据，以便在你的 `travis.yml` 文件中访问它们。进入 Travis CI 面板，选择当前项目 > **更多选项**> **设置**> **环境变量**。

设置环境变量的名称和值，以及希望它连接的分支。还可以设置值的隐私性。

### 设置

访问 [Travis-ci.com](https://Travis-ci.com)，使用首选提供商登录启用你的版本库。本指南使用 GitHub。然后，创建一个 `.travis.yml` 文件，并将以下内容复制到该文件中：

```yaml
---
filename: .travis.yml
---
language: node_js
node_js:
  - "18.0.0" # You can specify more versions of Node you want your CI process to support
branches:
  only:
    - travis-ci-test # Specify what branch you want your CI process to run on
install:
  - npm install

script:
  - npm run build # Switch this out with your build command or remove it if you don't have a build step
  - npx wrangler pages deploy dist --project-name=<PROJECT NAME>

env:
  - CLOUDFLARE_ACCOUNT_ID: { $CLOUDFLARE_ACCOUNT_ID }
  - CLOUDFLARE_API_TOKEN: { $CLOUDFLARE_API_TOKEN }
```

在上面的代码块中，你将语言指定为 `node_js`，并将值列为 `18.0.0`，因为 Wrangler v2 依赖于此 Node 版本或更高版本。你还设置了希望持续集成运行的分支。最后，在脚本部分输入 `PROJECT NAME`，你的 CI 进程就会如期运行。

你也可以使用任何 [`wrangler页面部署`选项](/workers/wrangler/commands/#deploy-1)修改 Wrangler 命令。