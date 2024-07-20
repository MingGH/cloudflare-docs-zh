---
pcx_content_type: concept
title: Pages Plugins
weight: 9
---

# 页面插件

Cloudflare 维护了许多官方 Pages 插件，供你在 Pages 项目中使用：

{{<directory-listing>}}

---

## Author a Pages Plugin

A Pages Plugin is a Pages Functions distributable which includes built-in routing and functionality. Developers can include a Plugin as a part of their Pages project wherever they chose, and can pass it some configuration options. The full power of Functions is available to Plugins, including middleware, parameterized routes, and static assets.

For example, a Pages Plugin could:

- Intercept HTML pages and inject in a third-party script.
- Proxy a third-party service's API.
- Validate authorization headers.
- Provide a full admin web app experience.
- Store data in KV or Durable Objects.
- Server-side render (SSR) webpages with data from a CMS.
- Report errors and track performance.

A Pages Plugin is essentially a library that developers can use to augment their existing Pages project with a deep integration to Functions.

## Use a Pages Plugin

Developers can enhance their projects by mounting a Pages Plugin at a route of their application. Plugins will provide instructions of where they should typically be mounted (for example, an admin interface might be mounted at `functions/admin/[[path]].ts`, and an error logger might be mounted at `functions/_middleware.ts`). Additionally, each Plugin may take some configuration (for example, with an API token).

---

## 静态表格示例

在本例中，你将构建一个页面插件，然后将其包含在一个项目中。

第一个插件应

- 拦截 HTML 表格。
- 将表单提交存储在 [KV](/kv/api/) 中。
- 用开发人员的自定义回复来响应提交。

### 1.创建新的页面插件

创建包含以下内容的 `package.json`：

```json
---
filename: package.json
---
{
  "name": "@cloudflare/static-form-interceptor",
  "main": "dist/index.js",
  "types": "index.d.ts",
  "files": ["dist", "index.d.ts", "tsconfig.json"],
  "scripts": {
    "build": "npx wrangler pages functions build --plugin --outdir=dist",
    "prepare": "npm run build"
  }
}
```

{{<Aside type="note">}}

`npx wrangler pages functions build`命令支持许多参数，包括

- `--plugin` 表示命令要构建一个 Pages 插件(而不是作为 Pages 项目一部分的 Pages 功能)。
- `outdir`，它允许你指定在哪里输出已构建的插件
- `--external`，可用于避免在插件中捆绑外部模块
- 参数 `--watch`告诉命令注意源文件的变化，并自动重建插件

有关可用参数的更多信息，请运行 `npx wrangler pages functions build --help`。

{{</Aside>}}

在我们的示例中，`dist/index.js` 将是插件的入口点。这是 Wrangler 使用 `npm run build` 命令生成的文件。将 `dist/` 目录添加到 `.gitignore`。

接下来，创建一个 `functions` 目录并开始编写插件。`functions `文件夹将被开发人员挂载到某个路径，因此请考虑如何构建你的文件。一般来说

- 如果希望插件在开发者选择的单一路由(例如 `/foo`)上运行，则创建一个 `functions/index.ts` 文件。
- 如果你希望你的插件被挂载并为特定路径(例如，`/admin/login` 和`/admin/dashboard`)以外的所有请求提供服务，请创建一个`functions/[[路径]].ts`文件。
- 如果想让插件拦截请求，但在其他函数或项目的静态资产上进行回退，请创建一个 `functions/_middleware.ts` 文件。

{{<Aside type="note" header="Do not include the mounted path in your Plugin">}}

你的插件不应在文件结构的任何地方使用挂载路径(例如，`/foo` 或 `/admin`)。开发人员可以自由选择挂载插件的位置，但可以在 `README.md`中建议如何挂载。

{{</Aside>}}

你可以根据需要自由使用不同的文件。插件的结构与现在 Pages 项目中的函数完全相同，不同之处在于处理程序会收到其参数对象的一个新属性 `pluginArgs`。该属性是开发人员在安装插件时传递的初始化参数。你可以用它来接收 API 标记、KV/Durable Object 命名空间或插件运行所需的任何其他内容。

回到静态表单的例子，如果你想拦截请求并覆盖 HTML 表单的行为，你需要创建一个 `functions/_middleware.ts`。这样，开发人员就可以在单个路由或整个项目中安装你的插件。

```typescript
---
filename: functions/_middleware.ts
---
class FormHandler {
  element(element) {
    const name = element.getAttribute('data-static-form-name')
    element.setAttribute('method', 'POST')
    element.removeAttribute('action')
    element.append(`<input type="hidden" name="static-form-name" value="${name}" />`, { html: true })
  }
}

export const onRequestGet = async (context) => {
  // We first get the original response from the project
  const response = await context.next()

  // Then, using HTMLRewriter, we transform `form` elements with a `data-static-form-name` attribute, to tell them to POST to the current page
  return new HTMLRewriter().on('form[data-static-form-name]', new FormHandler()).transform(response)
}

export const onRequestPost = async (context) => {
  // Parse the form
  const formData = await context.request.formData()
  const name = formData.get('static-form-name')
  const entries = Object.fromEntries([...formData.entries()].filter(([name]) => name !== 'static-form-name'))

  // Get the arguments given to the Plugin by the developer
  const { kv, respondWith } = context.pluginArgs


  // Store form data in KV under key `form-name:YYYY-MM-DDTHH:MM:SSZ`
  const key = `${name}:${new Date().toISOString()}`
  context.waitUntil(kv.put(name, JSON.stringify(entries)))

  // Respond with whatever the developer wants
  const response = await respondWith({ formData })
  return response
}
```

### 2. 输入你的页面插件

为了创造良好的开发体验，你应该考虑在插件中添加 TypeScript 类型。这样，开发人员就可以使用集成开发环境的自动完成功能，并确保包含你期望的所有参数。

在 `index.d.ts`中，导出一个接收`pluginArgs`并返回`PagesFunction`的函数。在静态表单示例中，你需要两个属性：`kv`(一个 KV 命名空间)和`respondWith`(一个接收带有`formData`属性(`FormData`)的对象并返回`Response`的`Promise`的函数)：

```typescript
---
filename: index.d.ts
---
export type PluginArgs = {
  kv: KVNamespace;
  respondWith: (args: { formData: FormData }) => Promise<Response>;
};

export default function (args: PluginArgs): PagesFunction;
```

### 3. 测试页面插件

我们仍在努力为页面插件作者创建良好的测试体验。请耐心等待我们完成所有工作。在此期间，你可以创建一个示例项目，并手动包含你的插件进行测试。

### 4. 发布页面插件

你可以随意发布你的插件。常用的方式包括在 [npm](https://www.npmjs.com/) 上发布，在我们的 [Developer Discord](https://discord.com/invite/cloudflaredev) 的 #what-i-built 或 #pages-discussions 频道中展示，以及在 [GitHub](https://github.com/) 上开源。

确保包含生成的 `dist/` 目录、你的类型 `index.d.ts` 以及包含开发人员如何使用插件的说明的 `README.md`。

---

### 5. Install your Pages Plugin

If you want to include a Pages Plugin in your application, you need to first install that Plugin to your project.

If you are not yet using `npm` in your project, run `npm init` to create a `package.json` file. The Plugin's `README.md` will typically include an installation command (for example, `npm install --save @cloudflare/static-form-interceptor`).

### 6. Mount your Pages Plugin

The `README.md` of the Plugin will likely include instructions for how to mount the Plugin in your application. You will need to:

1. Create a `functions` directory, if you do not already have one.
2. Decide where you want this Plugin to run and create a corresponding file in the `functions` directory.
3. Import the Plugin and export an `onRequest` method in this file, initializing the Plugin with any arguments it requires.

In the static form example, the Plugin you have created already was created as a middleware. This means it can run on either a single route, or across your entire project. If you had a single contact form on your website at `/contact`, you could create a `functions/contact.ts` file to intercept just that route. You could also create a `functions/_middleware.ts` file to intercept all other routes and any other future forms you might create. As the developer, you can choose where this Plugin can run.

A Plugin's default export is a function which takes the same context parameter that a normal Pages Functions handler is given.

```typescript
---
filename: functions/contact.ts
---
import staticFormInterceptorPlugin from "@cloudflare/static-form-interceptor";

export const onRequest = (context) => {
  return staticFormInterceptorPlugin({
    kv: context.env.FORM_KV,
    respondWith: async ({ formData }) => {
      // Could call email/notification service here
      const name = formData.get("name");
      return new Response(`Thank you for your submission, ${name}!`);
    },
  })(context);
};
```

### 7. Test your Pages Plugin

You can use `wrangler pages dev` to test a Pages project, including any Plugins you have installed. Remember to include any KV bindings and environment variables that the Plugin is expecting.

With your Plugin mounted on the `/contact` route, a corresponding HTML file might look like this:

```html
---
filename: public/contact.html
---

<!DOCTYPE html>
<html>
  <body>
    <h1>Contact us</h1>
    <!-- Include the `data-static-form-name` attribute to name the submission -->
    <form data-static-form-name="contact">
      <label>
        <span>Name</span>
        <input type="text" autocomplete="name" name="name" />
      </label>
      <label>
        <span>Message</span>
        <textarea name="message"></textarea>
      </label>
    </form>
  </body>
</html>
```

Your plugin should pick up the `data-static-form-name="contact"` attribute, set the `method="POST"`, inject in an `<input type="hidden" name="static-form-name" value="contact" />` element, and capture `POST` submissions.

### 8. Deploy your Pages project

Make sure the new Plugin has been added to your `package.json` and that everything works locally as you would expect. You can then `git commit` and `git push` to trigger a Cloudflare Pages deployment.

If you experience any problems with any one Plugin, file an issue on that Plugin's bug tracker.

If you experience any problems with Plugins in general, we would appreciate your feedback in the #pages-discussions channel in [Discord](https://discord.com/invite/cloudflaredev)! We are excited to see what you build with Plugins and welcome any feedback about the authoring or developer experience. Let us know in the Discord channel if there is anything you need to make Plugins even more powerful.

---

## 链接你的插件

最后，与一般的 Pages Functions 一样，也可以将 Plugins 链接起来，以组合不同的功能。在文件系统中较高位置定义的中间件将在其他处理程序之前运行，单个文件可以像这样在数组中将多个函数串联起来：

```typescript
---
filename: functions/admin/_middleware.ts
---
import sentryPlugin from "@cloudflare/pages-plugin-sentry";
import cloudflareAccessPlugin from "@cloudflare/pages-plugin-cloudflare-access";
import adminDashboardPlugin from "@cloudflare/a-fictional-admin-plugin";

export const onRequest = [
  // Initialize a Sentry Plugin to capture any errors
  sentryPlugin({ dsn: "https://sentry.io/welcome/xyz" }),

  // Initialize a Cloudflare Access Plugin to ensure only administrators can access this protected route
  cloudflareAccessPlugin({
    domain: "https://test.cloudflareaccess.com",
    aud: "4714c1358e65fe4b408ad6d432a5f878f08194bdb4752441fd56faefa9b2b6f2",
  }),

  // Populate the Sentry plugin with additional information about the current user
  (context) => {
    const email = context.data.cloudflareAccessJWT.payload?.email || "service user";

    context.data.sentry.setUser({ email });

    return next();
  },

  // Finally, serve the admin dashboard plugin, knowing that errors will be captured and that every incoming request has been authenticated
  adminDashboardPlugin(),
];
```