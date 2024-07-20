---
pcx_content_type: how-to
title: 利用 Cloudflare Tunnel 隧道预览本地项目
---

# 利用 Cloudflare 隧道预览本地项目

[Cloudflare Tunnel](/cloudflare-one/connections/connect-networks/)在你的基础架构中运行一个轻量级守护进程(`cloudflared`)，在你的源 Web 服务器和 Cloudflare 全球网络之间建立出站连接(隧道)。在实际应用中，你可以使用 Cloudflare Tunnel 允许远程访问本地计算机上运行的服务。它是 [Ngrok](https://ngrok.com)等流行工具的替代品，并通过 [TryCloudflare](/cloudflare-one/connections/connect-networks/do-more-with-tunnels/trycloudflare/) 服务提供免费、长期运行的隧道。

Cloudflare Pages 可为项目的新分支和提交提供唯一的[部署预览 URL](/pages/configuration/preview-deployments/)，而 Cloudflare Tunnel 可用于在开发过程中提供对本地运行的应用程序和服务器的访问。在本指南中，你将安装 Cloudflare Tunnel 并创建一个新隧道，以提供对本地运行应用程序的访问。开始使用 Cloudflare Tunnel 时，你需要一个 Cloudflare 账户。

## 安装 Cloudflare Tunnel 隧道

Cloudflare Tunnel 可安装在 Windows、Linux 和 macOS 上。要了解如何安装 Cloudflare Tunnel，请参阅 Cloudflare for Teams 文档中的 [Install cloudflared](/cloudflare-one/connections/connect-networks/downloads/) 页面。

在命令行中运行 `cloudflared --version` 确认 `cloudflared` 已正确安装：

```sh
$ cloudflared --version
cloudflared version 2021.5.9 (built 2021-05-21-1541 UTC)
```

## 运行本地服务

使用 Cloudflare Tunnel 启动和运行的最简单方法是在本地运行应用程序，例如 [React](/pages/framework-guides/deploy-a-react-site/) 或 [Svelte](/pages/framework-guides/deploy-a-svelte-site/) 网站。在使用这些框架开发应用程序时，它们通常会使用 "npm run develop "脚本或类似的脚本，挂载应用程序并在 "localhost "端口上运行。例如，流行的 `create-react-app` 工具会在端口 `3000` 上运行正在开发的 React 应用程序，使其可以通过 `http://localhost:3000` 地址访问。

## 启动 Cloudflare 隧道

在运行本地开发服务器的情况下，在新的命令行窗口中运行 `cloudflared tunnel` 即可实例化一个新的 Cloudflare 隧道，同时在 `--url` 标志中传递你的 `localhost` URL 和端口。`cloudflared` 将向命令行输出日志，包括带有隧道 URL 的横幅：

```sh
$ cloudflared tunnel --url http://localhost:3000
2021-07-15T20:11:29Z INF Cannot determine default configuration path. No file [config.yml config.yaml] in [~/.cloudflared ~/.cloudflare-warp ~/cloudflare-warp /etc/cloudflared /usr/local/etc/cloudflared]
2021-07-15T20:11:29Z INF Version 2021.5.9
2021-07-15T20:11:29Z INF GOOS: linux, GOVersion: devel +11087322f8 Fri Nov 13 03:04:52 2020 +0100, GoArch: amd64
2021-07-15T20:11:29Z INF Settings: map[url:http://localhost:3000]
2021-07-15T20:11:29Z INF cloudflared will not automatically update when run from the shell. To enable auto-updates, run cloudflared as a service: https://developers.cloudflare.com/argo-tunnel/reference/service/
2021-07-15T20:11:29Z INF Initial protocol h2mux
2021-07-15T20:11:29Z INF Starting metrics server on 127.0.0.1:42527/metrics
2021-07-15T20:11:29Z WRN Your version 2021.5.9 is outdated. We recommend upgrading it to 2021.7.0
2021-07-15T20:11:29Z INF Connection established connIndex=0 location=ATL
2021-07-15T20:11:32Z INF Each HA connection's tunnel IDs: map[0:cx0nsiqs81fhrfb82pcq075kgs6cybr86v9vdv8vbcgu91y2nthg]
2021-07-15T20:11:32Z INF +-------------------------------------------------------------+
2021-07-15T20:11:32Z INF |  Your free tunnel has started! Visit it:                    |
2021-07-15T20:11:32Z INF |    https://seasonal-deck-organisms-sf.trycloudflare.com     |
2021-07-15T20:11:32Z INF +-------------------------------------------------------------+
```

在此示例中，随机生成的 URL `https://seasonal-deck-organisms-sf.trycloudflare.com` 已创建并分配给你的隧道实例。在浏览器中访问此 URL 将显示应用程序正在运行，请求通过 Cloudflare 的全球网络，通过你机器上运行的隧道，安全地转发到 `localhost:3000`：

![Cloudflare 隧道渲染随机生成 URL 的示例](/images/pages/how-to/tunnel.png)

## 接下来的步骤

Cloudflare Tunnel 可通过多种方式进行配置，除了提供对正在开发的应用程序的访问外，还可用于其他用途。例如，你可以向 `cloudflared` 提供[配置文件](/cloudflare-one/connections/connect-networks/configure-tunnels/local-management/configuration-file/)，以添加更复杂的路由和隧道设置，而不仅仅是简单的`--url`标志。你还可以[附加 Cloudflare DNS 记录](/cloudflare-one/connections/connect-networks/routing-to-tunnel/dns/)到域或子域，以方便访问你本地计算机的长期隧道。

最后，通过结合 Cloudflare Access，你可以提供[对你的隧道的安全访问](/cloudflare-one/applications/configure-apps/self-hosted-apps/)，而不会暴露你的整个服务器，也不会影响安全性。请参阅 [Cloudflare for Teams 文档](/cloudflare-one/) 了解更多有关 Cloudflare 整套零信任工具的信息。