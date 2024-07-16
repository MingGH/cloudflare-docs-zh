---
updated: 2022-07-28
difficulty: Beginner
content_type: 📝 Tutorial
pcx_content_type: tutorial
title: Create a HTML form
tags: [Forms]
---

# 创建 HTML 表格

{{<tutorial-date-info>}}

在本教程中，你将使用纯 HTML 和 CSS 创建一个简单的`<form>`，并将其部署到 Cloudflare 页面。在此过程中，你将了解一些 HTML 表单属性以及如何在 Worker 中收集提交的数据。

{{<Aside type="note" header="MDN Introductory Series">}}

本教程将简要介绍 HTML 表单的基础知识。如需更深入的概述，请参阅 MDN 的 [Web 表单 - 使用用户数据](https://developer.mozilla.org/en-US/docs/Learn/Forms) 入门系列。

{{</Aside>}}

本教程将大量使用 Cloudflare Pages 和 [其 Workers 集成](/pages/functions/)。请参阅[入门指南](/pages/get-started/) 指南以熟悉该平台。

## 概览

在网络上，表单是用户与网络文档之间的常见交互点。表单允许用户输入数据，并通常将数据提交给服务器。一个表单至少由一个表单输入项组成，输入项可以是文本字段、下拉菜单、复选框等。

每个输入都应使用 [`name`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-name) 属性命名，以便服务器接收输入值时有一个可识别的名称。此外，随着 HTML5 的发展，表单元素还可以声明其他属性，以选择自动表单验证。可用的验证因输入类型而异；例如，接受电子邮件的文本输入(通过 [`type=email`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#input_types))可以确保值看起来像一个有效的电子邮件地址，数字输入(通过 `type=number`)将只接受整数或小数值(如果允许)，通用文本输入可以定义一个自定义的 [`pattern`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-pattern)来允许。不过，所有输入都可以声明某个值是否是 [`必要的`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/input#attr-required)。

下面是一个 HTML5 表单示例，其中定义了一些输入及其验证规则：

```html
<form method="POST" action="/api/submit">
  <input type="text" name="fullname" pattern="[A-Za-z]+" required />
  <input type="email" name="email" required />
  <input type="number" name="age" min="18" required />

  <button type="submit">Submit</button>
</form>
```

如果 HTML5 表单定义了验证规则，当用户尝试提交表单时，浏览器会自动检查所有规则。如果出现任何错误，提交将被阻止，浏览器会向用户显示错误信息，以供更正。只有在没有未解决的验证错误时，`<form>`才会将数据 `POST` 到`/submit`端点。整个过程是 HTML5 的原生过程，只需存在适当的表单和输入属性即可，无需 JavaScript。

表单元素还可以与[`<label>`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label)元素相关联，这样就可以清楚地描述每个输入。当然，这对视觉清晰度很有帮助，但由于 HTML 标记更加明确，因此也能带来更无障碍的用户体验。辅助技术可以直接从中受益；例如，屏幕阅读器可以显示哪个`<input>`是重点。当点击`<label>`时，其指定的表单输入会被聚焦，从而增加输入的激活区域。

要启用此功能，必须为每个输入创建一个 `<label>` 元素，并为每个 `<input>` 元素分配唯一的 `id` 属性值。该 `<label>` 还必须拥有一个 [`for`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/label#attr-for) 属性，以反映其输入的唯一 `id` 值。修改前面的代码段应能产生以下结果：

```html
<form method="POST" action="/api/submit">
  <label for="i-fullname">Full Name</label>
  <input id="i-fullname" type="text" name="fullname" pattern="[A-Za-z]+" required />

  <label for="i-email">Email Address</label>
  <input id="i-email" type="email" name="email" required />

  <label for="i-age">Your Age</label>
  <input id="i-age" type="number" name="age" min="18" required />

  <button type="submit">Submit</button>
</form>
```

{{<Aside type="note">}}

你的 `for` 和 `id` 值不必与上述值完全一致。你可以使用任何 `id` 值，只要它们在 HTML 文档中是唯一的即可。只有当 `for` 和 `id` 属性匹配时，`<label>` 才能与`<input>`链接。

{{</Aside>}}

当该 `<form>` 提交有效数据时，其数据内容将被发送到服务器。你可以通过在表单本身声明属性，自定义数据发送的方式和位置。如果你不提供这些细节，`<form>` 将把数据 GET 到当前 URL 地址，而这很少是你想要的行为。要解决这个问题，你至少需要定义一个包含目标 URL 地址的 [`action`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-action) 属性，但通常也建议你声明一个 [`method`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-method)，即使你重新声明的是默认的 `GET` 值。

默认情况下，HTML 表单以 `application/x-www-form-urlencoded` MIME 类型发送内容。该值将反映在 `Content-Type` HTTP 头信息中，接收服务器必须读取该头信息以确定如何解析数据内容。你可以通过 [`enctype`](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/form#attr-enctype) 属性自定义 MIME 类型。例如，要接受文件(通过 `type=file` )，必须将 `enctype` 改为 `multipart/form-data` 值：

```html
<form method="POST" action="/api/submit" enctype="multipart/form-data">
  <label for="i-fullname">Full Name</label>
  <input id="i-fullname" type="text" name="fullname" pattern="[A-Za-z]+" required />

  <label for="i-email">Email Address</label>
  <input id="i-email" type="email" name="email" required />

  <label for="i-age">Your Age</label>
  <input id="i-age" type="number" name="age" min="18" required />

  <label for="i-avatar">Profile Picture</label>
  <input id="i-avatar" type="file" name="avatar" required />

  <button type="submit">Submit</button>
</form>
```

由于 `enctype `发生了变化，浏览器也会改变向服务器发送数据的方式。HTTP 头信息 `Content-Type `将反映新的方式，HTTP 请求的正文也将符合新的 MIME 类型。接收服务器必须适应新格式，并调整其请求解析方法。

## 在线例子

本教程的其余部分将重点介绍如何在 Pages 上创建 HTML 表单，包括接收和解析表单提交的 Worker。

{{<Aside type="note" header="GitHub Repository">}}

此示例的源代码[可在 GitHub 上获取](https://github.com/cloudflare/submit.pages.dev)。它是一个实时 Pages 应用程序，还提供了 [实时演示](https://submit.pages.dev/)。

{{</Aside>}}

### 设置

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

### 标记

本例的表单相当简单明了。它包含一系列不同的输入类型，包括用于选择多个值的复选框。该表单也不包含任何验证，这样你就可以看到服务器是如何解释空值和/或缺失值的。

本示例项目只使用纯 HTML。你可以使用自己喜欢的 JavaScript 框架，但选择原始语言是为了简单和熟悉--所有框架都在抽象和/或产生类似的结果。

在项目目录中创建一个 `public/index.html`。所有前端资产都将存在于该`public`目录中，该`index.html`文件将作为网站的主页。

将以下内容复制并粘贴到 `public/index.html `文件中：

```html
<html lang="en">
  <head>
    <meta charset="utf8" />
    <title>Form Demo</title>
    <meta name="viewport" content="width=device-width,initial-scale=1" />
  </head>
  <body>
    <form method="POST" action="/api/submit">
      <div class="input">
        <label for="name">Full Name</label>
        <input id="name" name="name" type="text" />
      </div>

      <div class="input">
        <label for="email">Email Address</label>
        <input id="email" name="email" type="email" />
      </div>

      <div class="input">
        <label for="referers">How did you hear about us?</label>
        <select id="referers" name="referers">
          <option hidden disabled selected value></option>
          <option value="Facebook">Facebook</option>
          <option value="Twitter">Twitter</option>
          <option value="Google">Google</option>
          <option value="Bing">Bing</option>
          <option value="Friends">Friends</option>
        </select>
      </div>

      <div class="checklist">
        <label>What are your favorite movies?</label>
        <ul>
          <li>
            <input id="m1" type="checkbox" name="movies" value="Space Jam" />
            <label for="m1">Space Jam</label>
          </li>
          <li>
            <input id="m2" type="checkbox" name="movies" value="Little Rascals" />
            <label for="m2">Little Rascals</label>
          </li>
          <li>
            <input id="m3" type="checkbox" name="movies" value="Frozen" />
            <label for="m3">Frozen</label>
          </li>
          <li>
            <input id="m4" type="checkbox" name="movies" value="Home Alone" />
            <label for="m4">Home Alone</label>
          </li>
        </ul>
      </div>

      <button type="submit">Submit</button>
    </form>
  </body>
</html>
```

该 HTML 文档将包含一个表单，其中有几个字段供用户填写。由于表单中没有验证规则，因此所有字段都是可选的，用户可以提交一个空表单。在本示例中，这就是预期行为。

{{<Aside type="note" header="Optional content">}}

从技术上讲，只有 `<form>` 及其子元素是必要的。对于一个有效的 HTML 文档来说，`<head>` 和外层的`<html>` 和`<body>` 标记是可选的，并非严格意义上的必需。

此时的 HTML 页面也完全没有样式，而是依赖于浏览器的默认用户界面和调色板。页面样式完全可有可无，也不是表单运行所必需的。如果你想附加 CSS 样式表，可以[添加`<link>`元素](https://developer.mozilla.org/en-US/docs/Learn/CSS/First_steps/Getting_started#adding_css_to_our_document)。请参考已完成教程的[源代码](https://github.com/cloudflare/submit.pages.dev/blob/8c0594f48681935c268987f2f08bcf3726a74c57/public/index.html#L11) 以获得示例或灵感，唯一的要求是你的 CSS 样式表也必须位于 `public` 目录中。

{{</Aside>}}
### Worker

HTML 表单已完成，可随时部署。当用户提交该表单时，所有数据都将以 `POST` 请求的形式发送到 `/api/submit` URL。这是由于表单的 `method` 和 `action` 属性所致。不过，目前在 `/api/submit` 地址上还没有请求处理程序。现在你将创建它。

Cloudflare 页面提供 [功能](/pages/functions/) 功能，允许你定义和部署动态行为的 Worker。

函数链接到 `functions` 目录，并根据 `functions` 文件结构方便地构建 URL 请求处理程序。例如，`functions/about.js `文件将映射到`/about `URL，而 `functions/hello/[name].js `将处理`/hello/:name `URL 模式，其中`:name `是任何匹配的 URL 段。有关详细信息，请参阅 [Functions routing](/pages/functions/routing/) 文档。

要为 `/api/submit`定义处理程序，必须创建一个 `functions/api/submit.js` 文件。这意味着你的 `functions` 和 `public` 目录应是同级目录，整个项目结构类似于下面这样：

```txt
├── functions
│   └── api
│       └── submit.js
└── public
    └── index.html
```

`<form>` 将发送 `POST` 请求，这意味着 `functions/api/submit.js` 文件需要导出一个 `onRequestPost` 处理程序：

```js
---
filename: functions/api/submit.js
---
/**
 * POST /api/submit
 */
export async function onRequestPost(context) {
  // TODO: Handle the form submission
}
```

`context` 参数是一个对象，其中包含多个可能感兴趣的值。本例中只需要 [`Request`](/workers/runtime-apis/request/)对象，该对象可通过 `context.request` 键访问。

如前所述，`<form>` 在提交时默认为`application/x-www-form-urlencoded` MIME 类型。对于更高级的情况，则需要使用 `enctype="multipart/form-data"` 属性。幸运的是，这两种 MIME 类型都能被解析并视为 [`FormData`](https://developer.mozilla.org/en-US/docs/Web/API/FormData)。这意味着通过 Workers(包括页面函数)，你可以使用本地 [`Request.formData`](https://developer.mozilla.org/en-US/docs/Web/API/Request/formData) 解析器。

为说明起见，示例应用程序的表单处理程序将回复它收到的所有值。处理程序也必须始终返回一个 `Response` ：

```js
---
filename: functions/api/submit.js
---
/**
 * POST /api/submit
 */
export async function onRequestPost(context) {
  try {
    let input = await context.request.formData();
    let pretty = JSON.stringify([...input], null, 2);
    return new Response(pretty, {
      headers: {
        'Content-Type': 'application/json;charset=utf-8',
      },
    });
  } catch (err) {
    return new Response('Error parsing JSON content', { status: 400 });
  }
}
```

有了这个处理程序，示例就可以完全正常运行了。收到提交后，Worker 将回复一个包含`FormData`键值对的 JSON 列表。

但是，如果你想用 JSON 对象代替键值对(数组的数组)进行回复，则必须手动操作。最近，JavaScript 增加了 [`Object.fromEntries`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Object/fromEntries) 工具。这种方法在某些情况下效果很好；但是，示例 `<form>`中的`movies`核对表允许多个值。如果使用 `Object.fromEntries`，生成的对象将只保留一个`movies`值，而丢弃其余的。为避免这种情况，你必须编写自己的 `FormData` 到 `Object` 实用程序：

```js
---
filename: functions/api/submit.js
---
/**
 * POST /api/submit
 */
export async function onRequestPost(context) {
  try {
    let input = await context.request.formData();

    // Convert FormData to JSON
    // NOTE: Allows multiple values per key
    let output = {};
    for (let [key, value] of input) {
      let tmp = output[key];
      if (tmp === undefined) {
        output[key] = value;
      } else {
        output[key] = [].concat(tmp, value);
      }
    }

    let pretty = JSON.stringify(output, null, 2);
    return new Response(pretty, {
      headers: {
        'Content-Type': 'application/json;charset=utf-8',
      },
    });
  } catch (err) {
    return new Response('Error parsing JSON content', { status: 400 });
  }
}
```

最后一个代码段(如上)允许 Worker 保留所有值，并返回一个 JSON 响应，其中准确地表示了提交的`<form>`。

### 部署

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

- **Project name** – Your choice
- **Production branch** – `main`
- **Framework preset** – None
- **Build command** – None / Empty
- **Build output directory** – `public`

单击**保存并部署**按钮后，你的页面项目将开始首次部署。部署成功后，你将看到一个唯一的 `*.pages.dev` 子域和一个指向实时演示的链接。

在本教程中，你使用带有 Worker 集成的 Cloudflare Pages 构建并部署了一个网站及其后端逻辑。你创建了一个带有表单的静态 HTML 文档，该表单与 Worker 处理程序通信，以解析提交请求。

如果你想查看此应用程序的完整源代码，请访问 [GitHub](https://github.com/cloudflare/submit.pages.dev)。

## 相关资源

- [使用 Cloudflare Workers 为你的前端构建 API](/pages/tutorials/build-an-api with-pages-functions/)
- [使用 Airtable 处理表单提交](/workers/tutorials/handle-form-submissions-with-airtable/)