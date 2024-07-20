---
pcx_content_type: concept
title: Monorepos
---

# Monorepos

虽然有些应用程序是由单个资源库构建的，但 Pages 也支持具有更复杂设置的应用程序。单存储库是一个拥有多个子目录的存储库，每个子目录都包含自己的应用程序。

## 设置

你可以使用同一个版本库创建多个项目，[与创建任何其他 Pages 项目的方式相同](/pages/get-started/git-integration)。你可以选择更改项目的构建命令和/或根目录，告诉 Pages 你希望构建命令在哪里运行。即使连接到同一个版本库，所有项目名称也必须是唯一的。

## Builds

将 git 仓库连接到 Pages 时，默认情况下，对仓库中任何文件的修改都会触发 Pages 的构建。

![Monorepo 示例图](/images/pages/configuration/pages-path.png)

以上面的 `my-monorepo` 为例，它包含两个关联的 Pages 项目(`marketing-app` 和`ecommerce-app`)及其列出的依赖项。默认情况下，如果更改了 `marketing-app` 项目目录中的文件，那么也会触发 `ecommerce-app` 项目的联编，即使 `ecommerce-app` 及其依赖关系未发生变化。为避免重复编译，你可以包含或排除 [build watch paths](/pages/configuration/build-watch-paths) 或 [branches](/pages/configuration/branch-build-controls) 来指定 Pages 是否应该跳过某个项目的编译。

## Git 整合

为 Git 仓库中的每个项目创建独立的 Pages 项目后，除非在构建配置中指定，否则每次 Git 推送都会为所有连接的项目发布新的构建和部署。

GitHub 会为每个项目显示单独的注释，其中包含更新的项目和部署 URL(如果该分支有关联的拉取请求)。

### GitHub 检查运行和 GitLab 提交状态

如果你的版本库关联了多个项目，你的[GitHub 检查运行](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/collaborating-on-repositories-with-code-quality-features/about-status-checks#checks) 或[Gitlab 提交状态](https://docs.gitlab.com/ee/user/project/merge_requests/status_checks.html) 会在版本库中显示如下：

![GitHub检查运行](/images/pages/configuration/ghcheckrun.png)
![GitLab提交状态](/images/pages/configuration/glcommitstatus.png)

如果由于任何原因(如 CI 跳过、构建观察路径或分支部署控制)导致构建跳过，则不会显示检查运行/提交状态。

## Monorepo 管理工具：

虽然 Pages 没有为 monorepos 的依赖关系管理提供专门的工具，但你可以选择使用其他工具来帮助管理你的版本库。对于简单的子包管理，你可以使用 [npm](https://docs.npmjs.com/cli/v8/using-npm/workspaces)、[pnpm](https://pnpm.io/workspaces) 和 [Yarn](https://yarnpkg.com/features/workspaces) 工作区等工具。你还可以使用更强大的工具，如 [Turborepo](https://turbo.build/repo/docs)、[NX](https://nx.dev/getting-started/intro) 或 [Lerna](https://lerna.js.org/docs/getting-started) 来额外管理依赖关系和任务执行。

## 限制
* 必须使用 [Build System V2](/pages/configuration/language-support-and-tools)或更高版本，才能启用 monorepo 支持。
* 每个版本库最多可配置 5 个页面项目。如果你需要提高此限制，请联系 Cloudflare 帐户团队或使用[限制增加申请表](https://docs.google.com/forms/d/e/1FAIpQLSd_fwAVOboH9SlutMonzbhCxuuuOmiU1L_I5O2CFbXf_XXMRg/viewform)。