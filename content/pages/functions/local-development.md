---
pcx_content_type: how-to
title: 本地开发
weight: 6
---

# 本地开发

使用我们的 Wrangler 命令行界面 (CLI) 在本地运行你的 Pages 应用程序。

## 安装Wrangler

要开始使用 Wrangler，请参阅 [Install/Update Wrangler](/workers/wrangler/install-and-update/)。

## 在本地运行页面项目

在 Pages 上进行本地开发的主要命令是 `wrangler pages dev`。它可以让你本地运行 Pages 应用程序，包括提供静态资产和运行函数。

设置好静态资产文件夹后，运行以下命令开始本地开发：

```sh
$ npx wrangler pages dev <DIRECTORY-OF-ASSETS>
```

这将开始为你的 Pages 项目提供服务。你可以按 `b` 键在本地站点上打开浏览器(默认情况下可在 [http://localhost:8788](http://localhost:8788) 上打开)。

{{<Aside type="note">}}

如果为 Pages 项目配置了 [`wrangler.toml`](/pages/functions/wrangler-configuration/) 文件，就可以运行 [`wrangler pages dev`](/workers/wrangler/commands/#dev-1) 而无需指定目录。

{{</Aside>}}

### HTTPS 支持

要使用自签名证书通过 HTTPS 为本地开发服务器提供服务，可以 [通过 `wrangler.toml` 设置 `local_protocol`](/pages/functions/wrangler-configuration/#local-development-settings) 或向 [`wrangler pages dev`](/workers/wrangler/commands/#dev-1) 传递 `--local-protocol=https` 参数：

```sh
$ npx wrangler pages dev --local-protocol=https <DIRECTORY-OF-ASSETS>
```

## 将绑定附加到本地开发

要将绑定附加到本地开发，请参阅 [绑定](/pages/functions/bindings/) 并找到你想使用的 Cloudflare 开发人员平台资源。

## 通过 `wrangler.toml` 进行额外配置

如果在项目中使用 `wrangler.toml` 配置文件，则可以设置 dev 服务器的值，如端口`、`本地协议`、`IP `和 `端口`。更多信息，请阅读 [配置本地开发设置](/pages/functions/wrangler-configuration/#local-development-settings)。