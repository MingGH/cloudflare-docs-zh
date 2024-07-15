---
pcx_content_type: how-to
title: Redirecting www to domain apex
---

# 将 www 重定向到domain apex

了解如何将 `www` 子域重定向到你的 apex 域 (`example.com`)。

此设置假定你已经在 Pages 项目中附加了一个 [自定义域](/pages/configuration/custom-domains/)。

## 设置

将 `www` 子域重定向到你的domain apex：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com) 并选择你的账户。
2. 转到**批量重定向**。
3. [创建批量重定向列表](/rules/url-forwarding/bulk-redirects/create-dashboard/#1-create-a-bulk-redirect-list) 以下列内容为模型(但要适当替换数值)：

  {{<example>}}

  | 源 URL | 目标 URL | 状态 | 参数                                                                                              |
  | ---- | ----- | ------------ |-------------------------------------------------------------------------------------------------|
  | `www.example.com`    | `https://example.com` | `301`  | <ul><li>保留查询字符串</li><li>子路径匹配</li><li>保留路径后缀</li><li>包括子域</li></ul> |

  {{</example>}}

4. 使用刚才创建的列表 [创建批量重定向规则](/rules/url-forwarding/bulk-redirects/create-dashboard/#2-create-a-bulk-redirect-rule)。
5. 转到 **DNS**。
6. 使用以下值为 `www` 子域 [创建 DNS 记录](/dns/manage-dns-records/how-to/create-dns-records/#create-dns-records)：

  {{<example>}}

  | 类型 | 名称 | IPv4 地址 | 代理状态 |
  | ---- | ----- | ------------ | ------------ |
  | `A`    | `www` | `192.0.2.1`  | Proxied      |

  {{</example>}}

DNS 更改的传播可能需要一些时间，但一旦完成，就可以在终端运行以下命令。

```sh
$ curl --head -i https://www.example.com/
```

然后，检查输出，验证 `location` 标头和状态代码是否按配置设置。

## 相关资源

- [将 `*.pages.dev` 重定向到自定义域](/pages/how-to/redirect-to-custom-domain/)
- [使用批量重定向处理重定向](/rules/url-forwarding/bulk-redirects/)