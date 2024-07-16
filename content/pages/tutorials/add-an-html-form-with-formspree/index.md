---
updated: 2021-11-30
difficulty: Beginner
content_type: 📝 Tutorial
pcx_content_type: tutorial
title: Add an HTML form with Formspree
tags: [Forms]
---

# 使用 Formspree 添加 HTML 表单

{{<tutorial-date-info>}}

几乎每个网站，无论是简单的 HTML 作品集页面还是复杂的 JavaScript 应用程序，都需要一个表单来收集用户数据。[Formspree](https://formspree.io)是一种处理表单处理和存储的后端服务，允许开发人员在网站上加入表单，而无需编写服务器端代码或函数。

在本教程中，你将使用纯 HTML 和 CSS 创建一个`<form>`，并将其添加到 Cloudflare Pages 托管的静态 HTML 网站中。请参考 [入门指南](/pages/get-started/) 以熟悉该平台。你将使用 Formspree 收集提交的数据，并在有新提交时发送电子邮件通知，而不需要任何 JavaScript 或后台编码。

## 设置

首先，创建一个[新 GitHub 仓库](https://repo.new/)。然后在机器上创建一个新的本地目录，初始化 git，并将 GitHub 位置附加为远程目的地：

```sh
# create new directory
$ mkdir new-project
# enter new directory
$ cd new-project
# initialize git
$ git init
# attach remote
$ git remote add origin git@github.com:<username>/<repo>.git
# change default branch name
$ git branch -M main
```

现在你可以在创建的 `new-project` 目录中开始工作。

## 网站标记

在本示例项目中，你只需使用纯 HTML。主页将包含一个 "联系我们" 表单，可输入姓名、电子邮件地址和信息。

{{<Aside type="note">}}

表单代码改编自 HTML 表单教程。如需更深入地了解 HTML 表单的工作原理和其他学习资源，请参阅 [HTML 表单教程](/pages/tutorials/forms/)。

{{</Aside>}}

表格代码：

```html
<form method="POST" action="/">
  <label for="name">Full Name</label>
  <input id="name" type="text" name="name" pattern="[A-Za-z]+" required />

  <label for="email">Email Address</label>
  <input id="email" type="email" name="email" required />

  <label for="message">Message</label>
  <textarea id="message" name="message" required></textarea>

  <button type="submit">Submit</button>
</form>
```

`action`属性决定了表单数据的发送位置。稍后你将更新该属性，以便向 Formspree 发送表单数据。所有 `<input>` 标记都必须有一个唯一的`name`，以便捕获用户数据。`for` 和 `id` 值必须匹配，以便将`<label>`与相应的`<input>`链接起来，供屏幕阅读器等辅助工具使用。

{{<Aside type="note">}}

关于如何创建 HTML 表单，请参阅 [HTML 表单教程](/pages/tutorials/forms/)。

{{</Aside>}}

要在网站上添加此表单，首先要在项目目录中创建一个 `public/index.html`。`public`目录应包含所有前端资产，而 `index.html`  文件将作为网站的主页。

将以下内容复制并粘贴到包含上述表单的 `public/index.html` 文件中：

```html
<html lang="en">
  <head>
    <meta charset="utf8" />
    <title>Form Demo</title>
    <meta name="viewport" content="width=device-width,initial-scale=1" />
  </head>
  <body>
    <!-- the form from above -->

    <form method="POST" action="/">
      <label for="name">Full Name</label>
      <input id="name" type="text" name="name" pattern="[A-Za-z]+" required />

      <label for="email">Email Address</label>
      <input id="email" type="email" name="email" required />

      <label for="message">Message</label>
      <textarea id="message" name="message" required></textarea>

      <button type="submit">Submit</button>
    </form>
  </body>
</html>
```

现在你有一个 HTML 文档，其中包含一个 "联系我们 "表单，用户可以填写多个字段。但是，你还没有将 `action` 属性设置为可以处理表单数据的服务器。你将在本教程的下一节中进行设置。

{{<Aside type="note" header="GitHub Repository">}}

本示例的源代码[可在 GitHub 上获取](https://github.com/formspree/formspree-example-cloudflare-html)。它是一个实时 Pages 应用程序，也有 [实时演示](https://formspree-example-cloudflare-html.pages.dev/)。

{{</Aside>}}

## Formspree 后端

HTML 表单已经完成，但当用户提交该表单时，数据将以`POST`请求的形式发送到`/`URL。该 URL 上不存在处理数据的服务器，因此会导致错误。要解决这个问题，请创建一个新的 Formspree 表单，并将其唯一 URL 复制到表单的 `action` 中。

要创建 Formspree 表格，请注册[Formspree 账户](https://formspree.io/register)。

接下来，使用 **+ 新表单**按钮创建一个新表单。将其命名为 `Contact-us form`，并将收件人电子邮件更新为你希望接收表单提交的电子邮件。然后选择**创建表格**。

<GatsbyImage image="./new-form-dialog.png" alt="Creating a Formspree form" />

![创建 Formspree 表单](/images/pages/tutorials/new-form-dialog.png)

然后，你将看到如何整合新表格的说明。

![Formspree端点](/images/pages/tutorials/form-endpoint.png)

复制 `Form Endpoint` URL，并将其粘贴到上面创建的表单的 `action`属性中。

```html
<form method="POST" action="https://formspree.io/f/mqldaqwx">
  <!-- replace with your own formspree endpoint -->
</form>
```

现在，当你提交表单时，你应该会被重定向到一个 "谢谢 "页面。表单数据将提交到你在 [Formspree.io](https://formspree.io/) 上的账户。

你现在可以调整表单处理逻辑，更改 [重定向页面](https://help.formspree.io/hc/en-us/articles/360012378333--Thank-You-redirect)、更新 [通知电子邮件地址](https://help.formspree.io/hc/en-us/articles/115008379348-Changing-a-form-email-address)，或添加 [Google Sheets](https://help.formspree.io/hc/en-us/articles/360036563573-Use-Google-Sheets-to-send-your-submissions-to-a-spreadsheet)、[Slack](https://help.formspree.io/hc/en-us/articles/360045648933-Send-Slack-notifications) 等插件。

有关设置 Formspree 的更多帮助，请参阅以下资源：

- 有关 Formspree 的一般帮助，请参阅 [Formspree 帮助网站](https://help.formspree.io/hc/en-us)。
- 如需了解自己 HTML 表单的示例和灵感，请查看 [Formspree 表单库](https://formspree.io/library)。
- 有关将 Formspree 与 Next.js、Gatsby 和 Eleventy 等流行平台集成的技巧，请参阅 [Formspree 指南](https://formspree.io/guides)。

## 部署

现在你可以部署项目了。

如果你还没有这样做，请在 `git` 中保存你的进度，然后将提交推送到 GitHub 仓库：

```sh
# Add all files
$ git add -A
# Commit w/ message
$ git commit -m "working example"
# Push commit(s) to remote
$ git push -u origin main
```

你的作品现在位于 GitHub 仓库中，这意味着 Pages 也能访问它。

如果这是你的第一个 Cloudflare Pages 项目，请参阅 [开始](/pages/get-started/) 获取完整的设置指南。选择合适的 GitHub 仓库后，你必须使用以下构建设置配置项目：

- 项目名称**- 你的选择
- **生产分支**- `main`
- **框架预设**- 无
- **构建命令**- 无/空
- **构建输出目录**- `公共

选择**保存并部署**后，你的页面项目将开始首次部署。部署成功后，你将看到一个唯一的 `*.pages.dev` 子域和一个指向实时演示的链接。

在本教程中，你使用 Cloudflare Pages 和 Formspree 构建并部署了一个网站，以处理表单提交。你创建了一个带有表单的静态 HTML 文档，该表单与 Formspree 通信，以处理和存储提交请求并发送通知。

如果你想查看此应用程序的完整源代码，请访问 [GitHub](https://github.com/formspree/formspree-example-cloudflare-html)。

## 相关资源

- [使用 Formspree 添加 React 表单](/pages/tutorials/add-a-react-form-with-formspree/)
- [HTML 表格](/pages/tutorials/forms/)