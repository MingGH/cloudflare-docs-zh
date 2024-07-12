# Cloudflare 中文文档【AI翻译】

**[查看官方文档 →](https://developers.cloudflare.com/)**

[贡献官方文档](https://github.com/cloudflare/cloudflare-docs/blob/production/CONTRIBUTING.md)

贡献翻译文档：直接拉取这个项目到本地，修改后提PR就行。

## 安装

你必须在系统上安装 [HUGO](https://gohugo.io)，并在你的 `$PATH` 中作为全局二进制文件提供。大多数操作系统都支持 Hugo，请按照操作系统的相关[安装说明](https://gohugo.io/installation/)开始安装。

> **重要：** 本项目使用 `0.125.6+extended` 版本构建，这是所需的最低版本。你可以（可能）使用更新版本的 Hugo，但会受到 Hugo 改动的影响。

你还必须安装最新版本的 Node.js (18+)。你可以使用 Node 版本管理器 [Volta](https://github.com/volta-cli/volta) 安装最新版本的 Node 和 `npm`，后者是随 `node` 安装一起提供的软件包管理器。

```sh
$ curl https://get.volta.sh | bash
$ volta install node
```

最后，使用 npm 或其他软件包管理器为该项目安装 Node.js 依赖项：

```sh
$ npm install
```

## 本地开发

在对网站进行更改（包括任何内容更改）时，可通过运行以下命令来运行本地开发服务器：

```sh
$ npm run dev
```

这将生成一个服务器，可通过浏览器中的 `http://localhost:5173` 访问。此外，在项目中进行的任何更改（包括 `content/**`更改）都会自动重新加载浏览器选项卡，让你可以即时预览更改内容。

此外，该项目还包含一个 CI 步骤，用于确保代码风格的一致性。这适用于项目中的所有文件，包括 markdown (`*.md`) 文件，但不会影响内容本身或内容的输出显示。要查看样式错误，可以运行

```sh
$ npm run lint
```

## 使用声明

本仓库包含对[Cloudflare官方文档](https://github.com/cloudflare/cloudflare-docs)的翻译。原文档受知识共享署名 4.0 国际公共许可证 (Creative Commons Attribution 4.0 International License, CC BY 4.0) 保护。

### 使用许可
本仓库中的翻译文档也同样适用知识共享署名 4.0 国际公共许可证 (CC BY 4.0)。
