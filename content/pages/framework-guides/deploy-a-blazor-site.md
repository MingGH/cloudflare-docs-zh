---
pcx_content_type: how-to
title: Blazor
---

# Blazor

[Blazor](https://blazor.net)是一个 SPA 框架，可在浏览器中使用 C# 代码而非 JavaScript。在本指南中，你将使用 Blazor 构建一个网站，并使用 Cloudflare Pages 进行部署。

## 安装 .NET

Blazor 使用 C#。你需要最新版本的 [.NET SDK](https://dotnet.microsoft.com/download) 才能继续创建 Blazor 项目。如果你的系统中没有安装 SDK，请下载并运行安装程序。

## 创建新的 Blazor WASM 项目

Blazor有两种托管模式：[Blazor 服务器](https://learn.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-8.0#blazor-server)需要服务器为最终用户提供 Blazor 应用程序，而[Blazor WebAssembly](https://learn.microsoft.com/en-us/aspnet/core/blazor/hosting-models?view=aspnetcore-8.0#blazor-webassembly)则在浏览器中运行。Blazor 服务器与 Cloudflare 边缘网络模式不兼容，因此本指南仅使用 Blazor WebAssembly。

运行以下命令创建一个新的 Blazor WebAssembly (WASM) 应用程序：

```sh
$ dotnet new blazorwasm -o my-blazor-project
```

## 创建构建脚本

要进行部署，Cloudflare Pages 需要一种构建 Blazor 项目的方法。在项目的根目录下创建一个 `build.sh` 文件。用以下内容填充该文件(如果你使用的不是最新的 .NET SDK，请适当更新`.dotnet-install.sh`行)：

```
---
filename: build.sh
---
#!/bin/sh
curl -sSL https://dot.net/v1/dotnet-install.sh > dotnet-install.sh
chmod +x dotnet-install.sh
./dotnet-install.sh -c 8.0 -InstallDir ./dotnet
./dotnet/dotnet --version
./dotnet/dotnet publish -c Release -o output
```

你的 `build.sh` 文件必须是可执行的，这样编译命令才会起作用。运行 `chmod +x build.sh` 即可。

{{<render file="_tutorials-before-you-start.md">}}

## 创建一个 `.gitignore` 文件

创建`.gitignore `文件可确保只将需要的内容推送到 GitHub 仓库。运行以下命令创建`.gitignore`文件：

```sh
$ dotnet new gitignore
```

{{<render file="/_framework-guides/_create-github-repository.md">}}

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在 `账户主页 `中，选择 `**工作者和页面**`。
3. 选择 **创建应用程序**> **页面**> **连接到 Git**。

选择你创建的新 GitHub 仓库，并在**设置构建和部署**部分提供以下信息：

<div>

| Configuration option | Value            |
| -------------------- | ---------------- |
| Build command        | `./build.sh`     |

</div>

配置网站后，你可以开始首次部署。在部署之前，你应该能看到 Cloudflare 页面正在安装 `dotnet`、项目依赖项并构建网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目收到一个唯一的子域。
每次你向 Blazor 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。你还可以访问新拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

## 疑难解答

### 文件超过 25 MiB 限制

如果收到错误信息 `Error: Asset "/opt/buildhome/repo/output/wwwroot/_framework/dotnet.wasm" is over the 25MiB limit`，请执行以下操作之一解决此问题：

1. 通过以下 [指南](https://docs.microsoft.com/en-us/aspnet/core/blazor/performance?view=aspnetcore-6.0#minimize-app-download-size)缩小资产规模。

或者

2. 删除输出(`rm output/wwwroot/_framework/*.wasm`)中的`*.wasm`文件，并修改你的 Blazor 应用程序，改为 [加载 Brotli 压缩文件](https://docs.microsoft.com/en-us/aspnet/core/blazor/host-and-deploy/webassembly?view=aspnetcore-6.0#compression)。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Blazor">}}