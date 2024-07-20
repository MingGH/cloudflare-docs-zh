---
updated: 2021-07-27
difficulty: Beginner
pcx_content_type: tutorial
title: 从 GitHub 页面迁移基于 Jekyll 的网站
---

# 从 GitHub 页面迁移基于 Jekyll 的网站

在本教程中，你将学习如何将现有的[使用 Jekyll 的 GitHub Pages 网站](https://docs.github.com/en/pages/setting-up-a-github-pages-site-with-jekyll/about-github-pages-and-jekyll)迁移到 Cloudflare Pages。Jekyll 是与 GitHub Pages 一起使用的最流行的静态网站生成器之一，将 GitHub Pages 网站迁移到 Cloudflare Pages 只需几个简单的步骤。

本教程将为你提供指导：

1.  在项目配置中添加 GitHub Pages 使用的必要依赖项。
2.  创建一个新的 Cloudflare Pages 站点，与现有的 GitHub 仓库相连。
3.  在 Cloudflare 页面上构建和部署网站。
4. (可选)迁移自定义域。

包括构建时间在内，本教程应在 15 分钟内完成。

{{<Aside type="note">}}

如果你有一个未部署在 GitHub Pages 上的基于 Jekyll 的网站，请参阅[Jekyll 框架指南](/pages/framework-guides/deploy-a-jekyll-site/)。

{{</Aside>}}

## 开始之前

本教程假设

1.  你已有一个使用 [Jekyll](https://jekyllrb.com/) 的 GitHub Pages 网站。
2.  你对运行 Ruby 命令行工具有一定的了解，并且安装了 `gem` 和 `bundle`。
3.  知道如何使用一些基本的 Git 操作，包括 "添加"、"提交"、"推送 "和 "拉取"。
4.  你已阅读 Cloudflare 页面的 [入门](/pages/get-started/) 指南。

如果你的机器上没有安装 Rubygems (`gem`) 或 Bundler (`bundle`) ，请参阅 [Rubygems](https://rubygems.org/pages/download) 和 [Bundler](https://bundler.io/) 的安装指南。

## 准备 GitHub Pages 仓库

{{<Aside type="note">}}

如果你的 GitHub Pages 代码库中已经有了 `Gemfile` 和 `Gemfile.lock`，你可以完全跳过这一步。GitHub Pages 环境会默认设置一组未在`Gemfile`中明确指定的 Jekyll 插件。

{{</Aside>}}

你现有的基于 Jekyll 的版本库必须指定一个 "Gemfile"(Ruby 的依赖关系配置文件)，以便 Cloudflare Pages 在 [构建步骤](/pages/configuration/build-configuration/) 期间获取并安装这些依赖关系。

具体来说，你需要创建一个 `Gemfile`并安装 `github-pages`gem，其中包括 GitHub Pages 环境假定的所有依赖项。

```sh
---
header: Create a Gemfile
---
$ cd my-github-pages-repo
$ bundle init
```

打开为你创建的 `Gemfile`，在文件底部添加以下一行：

```ruby
---
header: Specifying the github-pages version
---
gem "github-pages", "~> 215", group: :jekyll_plugins
```

你的 `Gemfile`应与下相似：

```ruby
---
filename: Gemfile
---
# frozen_string_literal: true

source "https://rubygems.org"

git_source(:github) { |repo_name| "https://github.com/#{repo_name}" }

# gem "rails"
gem "github-pages", "~> 215", group: :jekyll_plugins
```

运行 `bundle update`，它会为你安装 `github-pages`gem，并创建一个包含已解决依赖版本的 `Gemfile.lock `文件。

```sh
---
header: Running bundle update
---
$ bundle update
# Bundler will show a lot of output as it fetches the dependencies
```

应该可以成功完成。如果没有，请检查是否准确复制了上面的 `github-pages` 行，并且没有注释掉前面的 `#`。

现在，你需要将这些文件提交到你的版本库，以便 Cloudflare Pages 在以下步骤中引用它们：

```sh
---
header: Commit Gemfile and Gemfile.lock
---
$ git add Gemfile Gemfile.lock
$ git commit -m "deps: added Gemfiles"
$ git push origin main
```

## 配置页面项目

现在，你的 GitHub Pages 项目已明确指定其依赖关系，你可以开始配置 Cloudflare Pages 了。此过程与 [部署 Jekyll 网站](/pages/framework-guides/deploy-a-jekyll-site/) 几乎相同。

{{<Aside type="note">}}

如果你是第一次配置 Cloudflare Pages 网站，请参阅 [Git 集成指南](/pages/get-started/git-integration/)，其中介绍了如何将现有 Git 仓库连接到 Cloudflare Pages。

{{</Aside>}}

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 "**设置构建和部署**"部分提供以下信息：

<div>

| 配置选项 | 值 |
| -------------------- | ------------- |
| 生产分支 | `main` | 生产分支|
| 生成命令 | `jekyll build` |
| 构建目录 | `_site`       |

</div>

配置好网站后，即可开始首次部署。在部署之前，你应该看到 Cloudflare 页面正在安装`jekyll`、项目依赖项并构建网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目获得一个唯一的子域。每次你向 Jekyll 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还将在新的拉取请求中访问 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

## 迁移自定义域名

如果你使用的是[带有 GitHub Pages 的自定义域名](https://docs.github.com/en/pages/configuring-a-custom-domain-for-your-github-pages-site)，你必须更新 DNS 记录以指向新的 Cloudflare Pages 部署。这需要你更新域名 DNS 提供商的 `CNAME` 记录，使其指向 `<your-pages-site>.pages.dev`，替换 `<your-username>.github.io`。

请注意，DNS 缓存过期和反映此更改可能需要一些时间，这取决于你最初创建记录时设置的 DNS TTL(生存时间)值。

详细步骤请参阅入门指南中的 [添加自定义域](/pages/configuration/custom-domains/#add-a-custom-domain) 部分。

# 下一步是什么？

- 了解如何使用 Cloudflare Workers 为你的 Pages 站点[自定义 HTTP 响应头](/pages/how-to/add-custom-http-headers/)。
- 了解如何 [回滚可能已损坏的部署](/pages/configuration/rollbacks/) 到以前的工作版本。
- [配置重定向](/pages/configuration/redirects/)，这样访问者就会被引导到你的 "规范 "自定义域。