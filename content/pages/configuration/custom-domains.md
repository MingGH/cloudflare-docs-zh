---
pcx_content_type: how-to
title: 自定义域
---

# 自定义域

部署页面项目时，你可能希望将自定义域（或子域）指向你的网站。

## 添加自定义域

添加自定义域：

1.登录 [Cloudflare 仪表板](https://dash.cloudflare.com/login)。
2.在**账户主页** > **Workers & Pages** 中选择你的账户。
3.选择页面项目 > **自定义域**。
4.选择 **设置域**。
5.提供你希望为 Cloudflare Pages 站点提供服务的域，然后选择 **继续**。

![通过 Cloudflare 面板为你的 Pages 项目添加自定义域名](/images/pages/platform/domains.png)

#### 添加自定义顶点域

如果要部署到一个 apex 域（例如，`example.com`），则需要将网站添加为 Cloudflare 区域，并 [配置你的名称服务器](#configure-nameservers)。

#### 配置名称服务器

要在 Pages 项目中使用自定义的 apex 域（例如，`example.com`），请[配置你的名称服务器以指向 Cloudflare 的名称服务器](/dns/zone-setups/full-setup/setup/)。如果你的名称服务器成功指向 Cloudflare，Cloudflare 将继续为你创建 CNAME 记录。

#### 添加自定义子域

如果你要部署到一个子域，则你的网站不必是 Cloudflare 区域。你需要[添加自定义 CNAME 记录](#add-a-custom-cname-record)将域名指向你的 Cloudflare 页面站点。要将你的 Pages 项目部署到自定义的 apex 域，该自定义域必须是你创建 Pages 项目的 Cloudflare 账户上的一个区域。

{{<Aside type="note">}}

如果该zone位于企业计划中，请确保在添加自定义域之前[解除区保留](/fundamentals/setup/account/account-security/zone-holds/#release-zone-holds)。区域保留将阻止自定义子域激活。

{{</Aside>}}

#### 添加自定义 CNAME 记录

如果你不想将你的名称服务器指向 Cloudflare，你必须创建一个自定义 CNAME 记录，以便使用 Cloudflare 页面的子域。登录 DNS 提供商后，为所需子域添加一条 CNAME 记录，例如 `shop.example.com`。此记录应指向你的自定义页面子域，例如，`<YOUR_SITE>.pages.dev`。

| 类型 |名称 |内容 |
| ------- | ------------------ | ----------------------- |
| `CNAME` | `shop.example.com` | `<YOUR_SITE>.pages.dev`` |

如果你的网站已经作为 Cloudflare 区域进行管理，那么在你确认 DNS 记录后，CNAME 记录将自动添加。

{{<Aside type="note">}}

为确保成功添加自定义域名，你必须通过上述[添加自定义域名](#add-a-custom-domain)流程。手动添加指向 Cloudflare Pages 站点的自定义 CNAME 记录 - 而不首先在 Cloudflare Pages 面板中关联域（或子域） - 将导致你的域无法解析到 CNAME 记录地址，并显示 [`522`错误](/support/troubleshooting/cloudflare-errors/troubleshooting-cloudflare-5xx-errors/#error-522-connection-timed-out)。

{{</Aside>}}

## 删除自定义域

要从 Pages 项目中分离自定义域，必须修改区域的 DNS 记录。

首先，登录 Cloudflare 仪表板 > 在**账户主页**中选择你的账户 > 选择你的网站 > **DNS**。

然后，在 **DNS** > **Records** 中：

1.  找到 Pages 项目的 CNAME 记录。
2.  选择 **编辑**。
3.  选择 **删除**。

接下来，在账户主页中，转到 ** 工作者和页面**：

1.  在**概览**中，选择你的页面项目。
2.  转到**自定义域**。
3.  选择自定义域名旁边的**三点图标** > **删除域名**。

完成这些步骤后，你的 Pages 项目将只能通过创建项目时选择的 `*.pages.dev` 子域访问。

## 禁止访问 `*.pages.dev` 子域

禁止访问项目提供的 `*.pages.dev` 子域：

1.在预览（`*.{project}.pages.dev`）上使用 Cloudflare Access。请参阅 [自定义预览部署访问](/pages/configuration/preview-deployments/#customize-preview-deployments-access)。

2.将与生产页面项目相关联的 `*.pages.dev` URL 重定向到自定义域。你可以使用账户级 [批量重定向](/rules/url-forwarding/bulk-redirects/) 功能将`*.pages.dev` URL 重定向到自定义域。

## 缓存

有关缓存的指导原则，请参阅 [缓存和性能](/pages/configuration/serving-pages/#caching-and-performance)。

## 已知问题

### CAA 记录

证书颁发机构授权 (CAA) 记录允许你限制向特定证书颁发机构 (CA) 颁发证书。

如果你的 CAA 记录不允许 Cloudflare 为你的自定义域签发证书，则在你的 Pages 项目中添加 [自定义域](/pages/configuration/custom-domains/) 时可能会出现问题。

要解决这个问题，请添加必要的 CAA 记录，以便 Cloudflare 为你的自定义域签发证书。

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

更多信息请参阅 [认证机构授权 (CAA) 常见问题解答](/ssl/edge-certificates/troubleshooting/caa-records/)。

### 将 DNS 条目从 Pages 中更改出来，然后再更改回来

一旦设置了自定义域名，如果你将 DNS 条目更改为指向其他内容（例如，你的源域名），自定义域名就会失效。如果你再将 DNS 条目改回指向你的自定义域，那么任何使用该 DNS 条目访问你网站的人都会出错，直到它再次激活为止。如果你想暂时将流量从 Pages 项目重定向，而不是更改 DNS 条目，最好使用 [Origin 规则](/rules/origin-rules/) 或 [redirect 规则](/rules/url-forwarding/single-redirects/create-dashboard/)。

## 相关资源

- [调试页面](/pages/configuration/debugging-pages/) - 查看部署页面项目时的常见错误。