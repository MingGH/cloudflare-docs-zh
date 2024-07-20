---
pcx_content_type: how-to
title: 调试页面
---

# 调试页面

在设置 Pages 项目时，你可能会遇到各种错误，导致无法成功部署网站。本指南概述了一些常见错误和解决方案。

## 检查构建日志

你可以在页面构建日志中查看构建错误。要访问构建日志，请

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com)。
2. 在**账户主页**，转到**工作者和页面**。
3. 在**概览**中，选择 Pages项目 > **查看构建**。

![登录 Cloudflare 控制面板后，按照上述说明访问构建日志](/images/pages/platform/pages-build-log.png)

构建日志中可能出现的错误包含在以下章节中。

### 初始化构建环境

此步骤中可能出现的错误可能是在 Git 集成过程中安装不当造成的。

在 GitHub 中修复此问题：
1. 登录 GitHub 账户。
2. 从用户图标进入**设置**> 在集成下找到**应用程序**。
3. 找到 **Cloudflare Pages**> **Configure**> 向下滚动并选择 **Uninstall**。
4. 在 Cloudflare 控制面板上重新授权你的 GitHub 用户/组织。

要在 GitLab 中解决这个问题
1. 登录 GitLab 账户。
2. 从用户图标 > **应用程序 > **首选项**。
3. 找到 **Cloudflare页面** > 向下滚动并选择 **撤销**。

请注意，要成功链接你的版本库，你需要一个 **Maintainer**或以上的角色，否则构建将失败。

### 克隆 git 仓库

此步骤中可能出现的错误可能是由于 Git 大文件存储空间(LFS)不足造成的。请参考 [GitHub](https://docs.github.com/en/billing/managing-billing-for-git-large-file-storage/viewing-your-git-large-file-storage-usage) 和 [GitLab](https://docs.gitlab.com/ee/topics/git/lfs/) 文档，检查 LFS 的使用情况。

请务必查看根目录下的 `.gitmodules` 文件，检查子模块配置。该文件需要包含 `path` 和 `url` 属性。

有效配置示例

```js
[submodule "example"]
	path = example/path
	url = git://github.com/example/repo.git
```

无效配置示例：

```js
[submodule "example"]
	path = example/path
```
或
```js
[submodule "example"]
        url = git://github.com/example/repo.git
```

### Building application

此步骤中可能出现的错误可能是由于 Pages 项目中的错误设置造成的。请检查你的构建命令、输出文件夹和环境变量中是否有任何不正确的配置。

### 部署到 Cloudflare 的全球网络

此步骤中可能出现的错误可能是由于页面功能配置不正确造成的。有关功能设置的更多信息，请参阅 [功能](/pages/functions/) 文档。

如果你没有使用 Functions 或已检查过你的 Functions 配置不包含任何错误，请查看 [Cloudflare 状态网站](https://www.cloudflarestatus.com/) 以了解可能导致构建失败的 Cloudflare 网络问题。

## `pages.dev` 与自定义域的区别

如果你的自定义域名通过 Cloudflare 进行代理(橙色云)，你的区域设置(如缓存)将适用。

如果你仅在自定义域上遇到框架(如 Nuxt.js)问题，请检查是否启用了 HTML 的自动最小化(已废弃)(登录 [Cloudflare 面板](https://dash.cloudflare.com/login) > **Speed**> **Optimization**> **Content Optimization**> **Auto Minify**)并禁用它。

如果你遇到新内容无法显示的问题，请转到 Cloudflare 面板中的 **Rules**> **Page Rules**，检查是否有启用了 **Cache Everything**的页面规则。如果存在，请删除此规则，因为页面会处理自己的缓存。

如果你在自定义域上遇到错误，但在 `pages.dev` 域上没有，请转到 Cloudflare 面板中的 **DNS**> **Records**，将项目的 DNS 记录设置为 **DNS Only**(灰色云)。如果错误仍然存在，请检查你的区域配置。

## 域名卡在验证中

如果你的 [自定义域](/pages/configuration/custom-domains/) 没有从 Cloudflare 面板中的 **Verifying**阶段移动，请参阅以下调试步骤。

### 阻止 HTTP 验证

页面使用 HTTP 验证，在验证过程中需要访问 HTTP 接口。如果有其他 Cloudflare 产品拦截(如 [Access](/cloudflare-one/policies/access/)、[a redirect](/rules/url-forwarding/)、[a Worker](/workers/) 等)，则无法完成验证。

要检查这一点，请在你的域中运行 `curl` 命令，点击 `/.owned/acme-challenge/randomstring`。例如
```sh
$ curl -I https://example.com/.well-known/acme-challenge/randomstring

HTTP/2 302
date: Mon, 03 Apr 2023 08:37:39 GMT
location: https://example.cloudflareaccess.com/cdn-cgi/access/login/example.com?kid=...&redirect_url=%2F.well-known%2Facme-challenge%2F...
access-control-allow-credentials: true
cache-control: private, max-age=0, no-store, no-cache, must-revalidate, post-check=0, pre-check=0
server: cloudflare
cf-ray: 7b1ffdaa8ad60693-MAN
```

在上面的示例中，你正在重定向到 Cloudflare Access(如 `Location` 标头所示)。在这种情况下，你需要禁用域上的 Access，直到域通过验证。域通过验证后，可以重新启用 Access。

你也需要为重定向规则或 Worker 示例做同样的事情。

### 缺少 CAA 记录

如果没有任何东西阻止 HTTP 验证，那么可能是缺少认证机构授权(CAA)记录。如果你禁用了 [Universal SSL](/ssl/edge-certificates/universal-ssl/)或使用外部提供商，就有可能出现这种情况。

要检查这一点，请在domain's apex (or zone，如果这是一个 [子域区域](/dns/zone-setups/subdomain-setup/))上运行 `dig`。例如

```sh
$ dig CAA example.com

; <<>> DiG 9.10.6 <<>> CAA example.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 59018
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 4096
;; QUESTION SECTION:
;example.com.		IN	CAA

;; ANSWER SECTION:
example.com.	300	IN	CAA	0 issue "amazon.com"

;; Query time: 92 msec
;; SERVER: 127.0.2.2#53(127.0.2.2)
;; WHEN: Mon Apr 03 10:15:51 BST 2023
;; MSG SIZE  rcvd: 76
```

在上述示例中，只有一条 CAA 记录允许亚马逊签发证书。

要解决这个问题，你需要添加以下 CAA 记录，这些记录允许 Cloudflare 使用的所有证书颁发机构 (CA) 颁发证书：
```
example.com.            300     IN      CAA     0 issue "comodoca.com"
example.com.            300     IN      CAA     0 issue "digicert.com; cansignhttpexchanges=yes"
example.com.            300     IN      CAA     0 issue "letsencrypt.org"
example.com.            300     IN      CAA     0 issue "pki.goog; cansignhttpexchanges=yes"
example.com.            300     IN      CAA     0 issuewild "comodoca.com"
example.com.            300     IN      CAA     0 issuewild "digicert.com; cansignhttpexchanges=yes"
example.com.            300     IN      CAA     0 issuewild "letsencrypt.org"
example.com.            300     IN      CAA     0 issuewild "pki.goog; cansignhttpexchanges=yes"
```

### Zone holds

[zone hold](/fundamentals/setup/account/account-security/zone-holds/) 将阻止页面为区域搁置下的主机名添加自定义域名。

要为有区域保留的主机名添加自定义域，请在自定义域设置过程中暂时 [释放区域保留](/fundamentals/setup/account/account-security/zone-holds/#release-zone-holds)。

自定义域成功完成后，你可以 [恢复区域保留](/fundamentals/setup/account/account-security/zone-holds/#enable-zone-holds)。

{{<Aside type="warning" header="Still having issues">}}

如果你已完成上述步骤，但 15 分钟后域名仍在验证中，请加入我们的 [Discord](https://discord.cloudflare.com) 寻求支持，或通过 [Support Portal](https://dash.cloudflare.com/?to=/:account/support) 联系我们的支持团队。

{{</Aside>}}

## 资源

如果你需要有关构建错误的其他指导，请联系你的 Cloudflare 账户团队(企业)，或参考 [支持中心](/support/contacting-cloudflare-support/) 以获得联系 Cloudflare 支持的指导。

你也可以在 [Cloudflare Developers Discord](https://discord.com/invite/cloudflaredev) 的页面部分提问。