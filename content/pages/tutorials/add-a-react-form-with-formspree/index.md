---
updated: 2021-11-30
difficulty: Beginner
content_type: 📝 Tutorial
pcx_content_type: tutorial
title: 使用 Formspree 添加 React 表单
tags: [Forms]
---

# 使用 Formspree 添加 React 表单

{{<tutorial-date-info>}}

几乎每个 React 网站都需要表单来收集用户数据。[Formspree](https://formspree.io/)是一种处理表单处理和存储的后端服务，允许开发人员在网站上包含表单，而无需编写服务器端代码或函数。

在本教程中，你将使用 React 创建一个`<form>`组件，并将其添加到使用 `create-react-app `构建的单页应用程序中。虽然你使用的是 `create-react-app` (CRA)，但其概念适用于任何 React 框架，包括 Next.js、Gatsby 等。你将使用 Formspree 收集提交的数据，并在有新的提交到达时发送电子邮件通知，而不需要任何服务器端编码。

你将把网站部署到 Cloudflare 页面。请参阅 [入门指南](/pages/get-started/) 以熟悉该平台。

## 设置

首先，在本地计算机上使用 `create-react-app` 创建一个新的 React 项目。然后创建一个 [new GitHub repository](https://repo.new/)，并将 GitHub 位置附加为远程目的地：

```sh
# create new project with create-react-app
$ npx create-react-app new-app
# enter new directory
$ cd new-app
# attach git remote
$ git remote add origin git@github.com:<username>/<repo>.git
# change default branch name
$ git branch -M main
```

现在，你可以修改你创建的 `new-app` 目录中的 React 应用程序。

## 前端代码

`create-react-app` 的起点包括一个简单的 Hello World 网站。你将添加一个 `联系我们 `表单，该表单接受姓名、电子邮件地址和信息。表单代码改编自 HTML 表单教程。有关 HTML 表单工作原理的更深入解释和其他学习资源，请参阅 [HTML 表单教程](/pages/tutorials/forms/)。

首先，创建名为 `ContactForm.js `的新 react 组件，并将其与 `App.js `一起放在 `src `文件夹中。

    project-root/
    ├─ package.json
    └─ src/
       ├─ ContactForm.js
       ├─ App.js
       └─ ...

接下来，你将使用 Formspree 的辅助库 [`@formspree/react`](https://github.com/formspree/formspree-react) 来构建表单组件。该库包含一个 `useForm` 钩子，用于简化处理表单提交事件和管理表单状态的过程。

用以下工具安装

```sh
$ npm install --save @formspree/react
```

然后将以下代码段粘贴到 `ContactForm.js` 文件中：

```jsx
import { useForm, ValidationError } from '@formspree/react';

export default function ContactForm() {
  const [state, handleSubmit] = useForm('YOUR_FORM_ID');

  if (state.succeeded) {
    return <p>Thanks for your submission!</p>;
  }

  return (
    <form method="POST" onSubmit={handleSubmit}>
      <label htmlFor="name">Full Name</label>
      <input id="name" type="text" name="name" required />
      <ValidationError prefix="Name" field="name" errors={state.errors} />

      <label htmlFor="email">Email Address</label>
      <input id="email" type="email" name="email" required />
      <ValidationError prefix="Email" field="email" errors={state.errors} />

      <label htmlFor="message">Message</label>
      <textarea id="message" name="message" required></textarea>
      <ValidationError prefix="Message" field="message" errors={state.errors} />

      <button type="submit" disabled={state.submitting}>
        Submit
      </button>
      <ValidationError errors={state.errors} />
    </form>
  );
}
```

目前，表单包含一个占位符 `YOUR_FORM_ID`。你可以在本教程稍后部分用自己的表单端点替换它。

`useForm`钩子会返回一个 `state `对象和一个 `handleSubmit `函数，并将其传递给 `onSubmit `表单属性。两者结合，就能通过 AJAX 提交表单数据，并根据收到的响应更新表单状态。

为清晰起见，该表单不包含任何样式，但在 GitHub 项目 (<https://github.com/formspree/formspree-example-cloudflare-react>) 中，你可以查看如何在表单中应用样式的示例。

{{<Aside type="note">}}

`ValidationError `组件是显示字段错误或一般表单错误(如果未提供 `field`属性)的错误信息的助手。有关验证的更多信息，请参阅 [Formspree React 文档](https://help.formspree.io/hc/en-us/articles/360055613373-The-Formspree-React-library#validation)。

{{</Aside>}}

要在网站上添加该表单，请导入该组件：

```jsx
import ContactForm from './ContactForm';
```

然后将表单作为react组件插入页面：

```jsx
<ContactForm />
```

例如，你可以更新 `src/App.js` 文件以添加表单：

```jsx
import ContactForm from './ContactForm'; // <-- import the form component
import logo from './logo.svg';
import './App.css';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>

        {/* your contact form component goes here */}
        <ContactForm />
      </header>
    </div>
  );
}

export default App;
```

现在，你有一个单页面应用程序，其中包含一个 "联系我们" 表单，用户可以填写多个字段。但是，你还没有设置表单提交到有效的表单端点。你将在 [下一节](#the-formspree-back-end)中进行设置。

{{<Aside type="note" header="GitHub repository">}}

此示例的源代码[可在 GitHub 上获取](https://github.com/formspree/formspree-example-cloudflare-react)。它是一个实时 Pages 应用程序，还提供了 [实时演示](https://formspree-example-cloudflare-react.pages.dev/)。

{{</Aside>}}

## Formspree 后端

React 表单已经完成，但是当用户提交该表单时，会出现 `Form not found`的错误。要解决这个问题，请创建一个新的 Formspree 表单，并将其唯一 ID 复制到表单的 `useForm` 调用中。

要创建Formspree表单，请注册[Formspree账户](https://formspree.io/register)。然后使用 **+ New form**按钮创建新表单。将新表单命名为 `Contact-us form`，并将收件人电子邮件更新为你希望接收表单提交的电子邮件。最后，选择**创建表格**。

创建 Formspree 表单](/images/pages/tutorials/react-new-form-dialog.png)

你将看到如何集成新表单的说明。复制表单的 `hashid`(URL 中最后 8 个字母数字字符)并将其粘贴到上文创建的 `ContactForm` 组件中的 `useForm` 函数中。

![新生成的表单端点，可复制到 ContactForm 组件中使用](/images/pages/tutorials/react-form-endpoint.png)

现在你的组件中应该有这样一行：

```jsx
const [state, handleSubmit] = useForm('mqldaqwx');

/* replace the random-like string above with your own form's ID */
```

现在，当你提交表单时，你将看到一条 "谢谢"信息。表单数据将提交到你在 [Formspree.io](https://formspree.io/) 上的账户。

在这里，你可以调整表单处理逻辑，更新[通知电子邮件地址](https://help.formspree.io/hc/en-us/articles/115008379348-Changing-a-form-email-address)，或添加[Google Sheets](https://help.formspree.io/hc/en-us/articles/360036563573-Use-Google-Sheets-to-send-your-submissions-to-a-spreadsheet)、[Slack](https://help.formspree.io/hc/en-us/articles/360045648933-Send-Slack-notifications)等插件。

有关设置 Formspree 的更多帮助，请参阅以下资源：

- 有关 Formspree 的一般帮助，请参阅 [Formspree 帮助网站](https://help.formspree.io/hc/en-us)。
- 有关在 React 中创建表格的更多帮助，请参阅 [formspree-react 文档](https://help.formspree.io/hc/en-us/articles/360055613373-The-Formspree-React-library)
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

如果这是你的第一个 Cloudflare Pages 项目，请参阅[入门指南](/pages/get-started/)以了解完整的操作步骤。选择合适的 GitHub 仓库后，你必须使用以下构建设置配置项目：

- 项目名称**- 你的选择
- **生产分支**- `main`
- 框架预设**- 创建 React 应用程序
- 构建命令**- `npm run build`.
- **构建输出目录**- `build`.

选择**保存并部署**后，你的页面项目将开始首次部署。部署成功后，你将看到一个唯一的 `*.pages.dev` 子域和一个指向实时演示的链接。

## 使用表单环境变量

有时，设置两个表单会很有帮助，一个用于开发，一个用于生产。这样，你就可以在不破坏生产数据集或向客户发送测试通知的情况下开发和测试表单。

要设置生产和开发表单，首先要在 Formspree 中创建第二个表单。将此表单命名为 `联系我们测试`，并注明表单的 [`hashid`](https://help.formspree.io/hc/en-us/articles/360015130174-Getting-your-form-s-hashid-)。

然后更改 `ContactForm.js` 文件中的 `useForm` 钩子，使其使用环境变量而不是字符串初始化：

```jsx
const [state, handleSubmit] = useForm(process.env.REACT_APP_FORM_ID);
```

在 Cloudflare Pages 项目设置中，为生产环境和预览环境添加 `REACT_APP_FORM_ID` 环境变量。在生产环境中使用原始表单的 `hashid`，在预览环境中使用新测试表单的 `hashid`：

![编辑生产环境和预览环境中的环境变量选项](/images/pages/tutorials/env-vars.png)

现在，当你将更改提交并推送到 git 仓库的某个分支时，将创建一个新的预览应用程序，其中的表单将提交到测试表单 URL。不过，你的生产网站将继续提交到原始表单 URL。

{{<Aside type="note">}}w

Create React App 使用前缀 `REACT_APP_` 来指定前端 JavaScript 代码可访问的环境变量。不同的框架会使用不同的前缀来公开环境变量。例如，在 Next.js 中，前缀是 `NEXT_PUBLIC_`。请查阅前端框架的文档，以确定如何从 React 代码中访问环境变量。

{{</Aside>}}

在本教程中，你使用 Cloudflare Pages 和 Formspree 构建并部署了一个网站，以处理表单提交。你创建了一个带有表单的 React 应用程序，该表单与 Formspree 通信，以处理和存储提交请求并发送通知。

如果你想查看此应用程序的完整源代码，请访问 [GitHub](https://github.com/formspree/formspree-example-cloudflare-react)。

## 相关资源

- 使用 Formspree 添加 HTML 表单](/pages/tutorials/add-an-html-form-with-formspree/)
- HTML 表格](/pages/tutorials/forms/)