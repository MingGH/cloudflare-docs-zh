---
pcx_content_type: concept
title: Preview deployments
---

# 预览部署

通过预览部署，你可以预览项目的新版本，而无需将其部署到生产环境中。查看预览部署：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/login) 并选择你的账户。
2. 在**账户主页**，选择**工作者和页面**。
3. 选择项目并找到要查看的部署。

每次你在 GitHub 仓库上打开一个新的拉取请求时，Cloudflare Pages 都会创建一个唯一的预览 URL，并随着你不断向分支推送新的提交内容而保持更新。只有当拉取请求源自版本库本身时，才会出现这种情况。

例如，如果你有一个名为 `user-example` 的版本库连接到 Pages，这将为你提供一个 `user-example.pages.dev` 子域。如果 `main` 是你的默认分支，那么对 `main` 分支的任何提交都会更新你的 `user-example.pages.dev` 内容，以及附加到该项目的任何 [自定义域](/pages/configuration/custom-domains/)。

![用户示例版本库的部署状态和预览](/images/pages/platform/preview-deployment-mergedone.png)

例如，在开发 `user-example` 时，你可能会将新更改推送到 `development` 分支。

在本示例中，创建新的 `development` 分支后，Pages 会自动为这些更改生成一个预览部署，地址为 `373f31e2.user-example.pages.dev` - 其中 `373f31e2` 是随机生成的哈希值。

你创建的每个新分支都会在你的 `pages.dev` 子域前收到一个随机生成的新散列值。

![用户示例版本库新生成的预览部署链接和状态](/images/pages/platform/preview-deployment-generated.png)

对 `development `分支的任何其他更改都将继续更新这个 `373f31e2.user-example.pages.dev` 预览地址，直到 `development `分支与 `main `生产分支合并。

任何自定义域以及你的 `user-example.pages.dev` 站点都不会受到预览部署的影响。

## 自定义预览部署访问

你可以使用 [Cloudflare Access](/cloudflare-one/policies/access/)来管理对部署预览的访问。默认情况下，这些部署 URL 是公开的。启用访问策略将限制只有你的 Cloudflare 账户才能查看项目部署。

启用后，你可以 [设置多用户账户](/fundamentals/setup/manage-members/)，允许团队中的其他成员查看预览部署。

默认情况下，预览部署已启用并可公开访问。在项目设置中，你可以要求访问者通过身份验证才能查看预览部署。这样，你就可以将这些预览部署的访问权限锁定给你的队友、组织或你通过 [访问策略](/cloudflare-one/policies/) 指定的任何人。

在 Cloudflare Access 后面保护你的预览部署：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/login)。
2. 在 `账户主页 `中，选择 `**工作者和页面**`。
3. 在**概览**中，选择你的页面项目。
4. 转到 **设置**> **常规**> 并选择 **启用访问策略**。

请注意，这只会保护你的预览部署(例如，`373f31e2.user-example.pages.dev` 和其他随机生成的预览链接)，而不会保护你的`*.pages.dev` 域或自定义域。

{{<Aside note="warning">}}

如果你想在预览部署的同时启用`*.pages.dev`域和自定义域的访问权限，请查看 [已知问题](/pages/platform/known-issues/#enable-access-on-your-pagesdev-domain) 以获取说明。

{{</Aside>}}

## 预览别名

发布预览部署时，会给它一个基于哈希值的唯一地址，例如，`<hash>.<project>.pages.dev`。这些都是原子地址，将来可能会被访问。不过，Pages 也会为 `git` 分支的名称创建一个别名，并对其进行更新，以便该别名始终映射到该分支的最新提交。

例如，如果你将更改推送到 `development `分支(与生产环境无关)，那么 Pages 将部署到 `abc123.<project>.pages.dev`，并将其别名为 `development.<project>.pages.dev`。之后，你可能会向 `development` 分支推送新工作，从而创建 `xyz456.<project>.pages.dev` 部署。此时，`development.<project>.pages.dev `别名指向 `xyz456 `部署，但 `abc123.<project>.pages.dev `仍可直接访问。

分支名称别名小写，非字母字符用连字符代替，例如，`fix/api `分支创建了 `fix-api.<project>.pages.dev `别名。

要查看 Pages 项目中的分支别名，请为任何预览部署选择**查看构建**。**部署详情**将显示与该部署相关的所有别名。

你可以通过 [将自定义域添加到分支](https://developers.cloudflare.com/pages/how-to/custom-branch-aliases/) 将预览别名附加到自定义域。