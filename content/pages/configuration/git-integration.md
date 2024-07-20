---
pcx_content_type: concept
title: Git 整合
---

# Git 整合

Cloudflare 支持将 Cloudflare Pages 连接到 GitHub 和 GitLab 资源库，以查找项目的新更改。Pages 目前不支持 GitHub 或 GitLab 的自托管实例。

## 自定义分支

假设你有一个自定义的 Git 工作流程，使用特定的分支来代表项目的生产构建。在这种情况下，你可以在 Pages 面板中创建(或管理现有)项目时指定一个自定义分支，方法是进入 **设置**> **构建和部署**，然后点击 **配置生产部署**按钮。你可以在**生产分支**下的下拉菜单中将生产分支更改为任何其他分支。

你还可以使用[预览部署](/pages/configuration/preview-deployments/)，在合并到 `production`之前预览项目新版本的外观。此外，Pages 还允许你使用[分支构建控制](/pages/configuration/branch-build-controls/)来配置构建和部署哪些预览分支。

要在 Pages 项目中对此进行配置，请转到 **设置**> **构建和部署**> **配置预览部署**，然后选择 **自定义分支**。在这里，你可以在提供的配置字段中指定希望包含或排除在自动部署之外的分支。要了解更多信息，请参阅 [branch build controls](/pages/configuration/branch-build-controls/) 文档。


## 组织访问

你可以将你的开源团队、公司或 GitHub 和 GitLab 上的辅助项目部署到 Cloudflare 页面。

### GitHub

授权 Cloudflare Pages 访问 GitHub 账户时，你可以指定访问你的个人账户或你在 GitHub 上所属的组织。为了能将 Cloudflare Pages 安装添加到该组织，你的用户账户必须是该组织的所有者或具有相应的角色(即 GitHub 应用程序管理器角色)。有关这些角色的更多信息，请参阅 [GitHub 文档](https://docs.github.com/en/organizations/managing-peoples-access-to-your-organization-with-roles/roles-in-an-organization#github-app-managers)。

{{<Aside type="warning" header="GitHub security consideration">}}

一个 GitHub 帐户只能指向一个 Cloudflare 帐户，但这并不强制执行。如果你要通过 GitHub 为你的组织设置 Cloudflare，Cloudflare 建议你将应用程序的范围限制在你设置项目时打算使用 Pages 构建的存储库中。你可以在构建更多应用程序时修改这些权限。

{{</Aside>}}

### GitLab

授权 Cloudflare Pages 访问你的 GitLab 帐户后，你将自动允许 Cloudflare Pages 访问你的 GitLab 帐户可访问的组织、组和命名空间。对这些组织和组的访问管理由 GitLab 负责。

## 删除对 GitHub 账户的访问权限

你可以通过查看 GitHub 上的[**应用程序**页面](https://github.com/settings/installations)来移除 Cloudflare Pages 对你 GitHub 账户的访问权限。请注意，移除对 GitHub 的访问权限也将禁用新的构建，但你网站的最后一次构建将继续通过 Cloudflare Pages 托管。

## 删除对 GitLab 账户的访问权限

你可以通过导航至**用户设置**> **应用程序**> **授权应用程序**，取消 Cloudflare Pages 对你 GitLab 账户的访问权限。找到名为 Cloudflare Pages 的应用程序，然后选择**撤销**按钮撤销访问权限。

## 通过提交信息跳过特定构建

无需任何配置，你可以选择临时跳过部署。在提交信息中添加`[CI Skip]`, `[CI-Skip]`, `[Skip CI]`, `[Skip-CI]`或`[CF-Pages-Skip]`标记作为前缀，Pages 就会省略该部署。前缀不区分大小写。

## 重新安装 Git

当遇到与 Git 集成相关的问题时，一个潜在的故障排除步骤是尝试卸载和重新安装与 Cloudflare Pages 安装相关的 GitHub 或 GitLab 应用程序。各 Git 提供商的流程如下。

### GitHub

1. 访问 GitHub 上的安装设置页面：
    1. 个人账户的 "https://github.com/settings/installations"。
    1. 组织账户的 "https://github.com/organizations/<YOUR_ORGANIZATION_NAME>/settings/installations"。
2. 如果已安装 Cloudflare Pages，请单击**配置**，然后单击**卸载 "Cloudflare Pages "**(如果没有安装 "Cloudflare Pages"，用户无需执行任何操作)。
3. 返回位于 `https://dash.cloudflare.com/[YOUR_ACCOUNT_ID]/workers-and-pages` 的 **Workers & Pages**总览页面。点击 **Create application**> **Pages**> **Connect to Git**
4. 点击 **+ 添加账户**按钮，点击要添加的 GitHub 账户，然后点击 **安装和授权**。
5. 你应该会被重定向到创建项目页面，账户列表中会出现你的 GitHub 账户或组织。
6. 尝试对之前已损坏的项目进行新的部署。

### GitLab

1. 访问 GitLab 上的应用程序设置页面，网址是： https://gitlab.com/-/profile/applications
2. 点击 Cloudflare Pages 安装中的 "撤销 "按钮(如果存在)。
3. 返回位于 `https://dash.cloudflare.com/[YOUR_ACCOUNT_ID]/workers-and-pages` 的 **Workers & Pages**总览页面。点击 **Create application**> **Pages**> **Connect to Git**
4. 选择顶部的 "**GitLab**"选项卡，点击 "**+ 添加账户**"按钮，点击要添加的 GitLab 账户，然后在题为 "授权 Cloudflare Pages 使用你的账户？"的模式对话框中点击 "**授权**"。
5. 你应该会被重定向到创建项目页面，账户列表中会出现你的 GitHub 账户或组织。
6. 尝试对之前已损坏的项目进行新的部署。

## 疑难解答

### 项目创建

#### `This repository is being used for a Cloudflare Pages project on a different Cloudflare account.`

禁止在不同的 Cloudflare 账户中使用同一个 Github/Gitlab 版本库。要使用该 Cloudflare 账户中 Pages 项目的版本库，应删除在其他 Cloudflare 账户中使用该版本库的任何 Pages 项目。

### 部署

如果遇到任何与部署相关的问题或失败，请检查项目控制面板，查看是否列出了任何 SCM 安装警告，如下图所示。

![在页面项目的设置中暂停部署](/images/pages/platform/git.dashboard-error.png)

要解决 Cloudflare 页面控制面板中显示的任何错误，请按照以下步骤操作。

#### `This project is disconnected from your Git account, this may cause deployments to fail.`

要解决此问题，请针对适用的 SCM 提供商，按照上述 [重新安装 Git 安装](/pages/configuration/git-integration/#reinstall-a-git-installation) 部分提供的步骤操作。如果卸载和重新安装后问题仍然存在，请联系技术支持。

#### `Cloudflare Pages is not properly installed on your Git account, this may cause deployments to fail.`

要解决此问题，请针对适用的 SCM 提供商，按照上述 [重新安装 Git 安装](/pages/configuration/git-integration/#reinstall-a-git-installation) 部分提供的步骤操作。如果卸载和重新安装后问题仍然存在，请联系技术支持。

#### `The Cloudflare Pages installation has been suspended, this may cause deployments to fail.`

进入 GitHub 安装设置：

* 个人账户的 "https://github.com/settings/installations
* `https://github.com/organizations/<YOUR_ORGANIZATION_NAME>/settings/installations` 用于组织账户

在 Cloudflare Pages 应用程序上单击**配置**。向下滚动到页面底部，然后单击**取消暂停**，允许 Cloudflare Pages 今后进行部署。

#### `The project is linked to a repository that no longer exists, this may cause deployments to fail.`

你可能已删除或转移了与此 Cloudflare Pages 项目相关的版本库。对于已删除的版本库，你需要使用未删除的版本库创建一个新的 Cloudflare Pages 项目。对于已转移的版本库，你可以将版本库转移回原始 Git 账户，或者使用已转移的版本库创建一个新的 Cloudflare Pages 项目。

#### `The repository cannot be accessed, this may cause deployments to fail.`

你可能已将此版本库排除在安装的版本库访问设置之外。  转到 GitHub 安装设置：

* 个人账户的 "https://github.com/settings/installations
* `https://github.com/organizations/<YOUR_ORGANIZATION_NAME>/settings/installations` 用于组织账户

单击 Cloudflare Pages 应用程序上的**配置**。在**版本库访问**下，确保与 Cloudflare Pages 项目相关的版本库包含在列表中。

#### `There is an internal issue with your Cloudflare Pages Git installation.`

这是 Cloudflare Pages SCM 系统的内部错误。你可以尝试[重新安装 Git 安装](/pages/configuration/git-integration/#reinstall-a-git-installation)，但如果问题仍然存在，请[联系支持](/support/contacting-cloudflare-support/)。

## 相关资源

* [分支构建控制](/pages/configuration/branch-build-controls/#production-branch-control) - 控制要自动部署到哪些环境和分支。