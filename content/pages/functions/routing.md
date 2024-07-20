---
pcx_content_type: reference
title: 功能路由
weight: 2
---

# 功能路由[Functions routing]

函数使用基于文件的路由。你的 `/functions` 目录结构决定了你的函数将运行的指定路由。你可以根据项目用例的需要创建不同层级的 `/functions`目录。请查看以下目录：

```
|---- …
|---- functions
      |___ index.js
      |___ helloworld.js
      |___ howdyworld.js
      |___ fruits
            |___ index.js
            |___ apple.js
            |___ banana.js
```

将根据上述文件结构生成以下路由。这些路由将 URL 模式映射到`/functions`文件，当访问者访问该 URL 时，将调用该文件：

| File path                   | Route                     |
|-----------------------------|---------------------------|
| /functions/index.js         | example.com               |
| /functions/helloworld.js    | example.com/helloworld    |
| /functions/howdyworld.js    | example.com/howdyworld    |
| /functions/fruits/index.js  | example.com/fruits        |
| /functions/fruits/apple.js  | example.com/fruits/apple  |
| /functions/fruits/banana.js | example.com/fruits/banana |

{{<Aside type="note" header="Trailing slash">}}

尾部斜线为可选项。`/foo` 和 `/foo/` 都将被路由到 `/functions/foo.js` 或 `/functions/foo/index.js`。如果你的项目同时有 `/functions/foo.js` 和 `/functions/foo/index.js` 文件，则 `/foo` 和 `/foo/` 将路由到 `/functions/foo/index.js`。

{{</Aside>}}

如果没有匹配到 Function，则会返回到静态资产(如果有的话)。否则，Function 将返回到页面静态资产的[默认路由行为](/pages/configuration/serving-pages/)。

## 动态路由

通过动态路由，你可以将 URL 与参数化网段进行匹配。这对于构建动态应用程序非常有用。你可以通过更改文件名来接受映射到单一路径的动态值。

### 单一路径分段

要创建动态路由，请在文件名前后加上一组括号，例如 `/users/[user].js`。这样做的目的是为单个路径段创建一个占位符：

| Path               | Matches? |
|--------------------|----------|
| /users/nevi        | Yes      |
| /users/daniel      | Yes      |
| /profile/nevi      | No       |
| /users/nevi/foobar | No       |
| /nevi              | No       |

### 多路径段

通过在文件名周围放置两组括号--例如，`/users/[[user]].js`--就可以匹配`/users/`之后的任何路由深度：

| Path                  | Matches? |
|-----------------------|----------|
| /users/nevi           | Yes      |
| /users/daniel         | Yes      |
| /profile/nevi         | No       |
| /users/nevi/foobar    | Yes      |
| /users/daniel/xyz/123 | Yes      |
| /nevi                 | No       |

{{<Aside type="note" header="Route specificity">}}

较具体的路由(通配符较少的路由)优先于较不具体的路由。

{{</Aside>}}

#### 动态路由示例

查看以下 `/functions/` 目录结构：

```
|---- …
|---- functions
      |___ date.js
      |___ users/
            |___ special.js
            |___ [user].js
            |___ [[catchall]].js
```

以下请求将匹配以下文件：

| Request               | File                                             |
|-----------------------|--------------------------------------------------|
| /foo                  | Will route to a static asset if one is available.|
| /date                 | /date.js                                         |
| /users/daniel         | /users/[user].js                                 |
| /users/nevi           | /users/[user].js                                 |
| /users/special        | /users/special.js                                |
| /users/daniel/xyz/123 | /users/[[catchall]].js                           |


与占位符 (`[user]`)匹配的 URL 段将出现在请求 [`context`](/pages/functions/api-reference/#eventcontext)对象中。可使用 [`context.params`](/pages/functions/api-reference/#params)对象查找与给定文件名占位符匹配的值。

对于匹配单个 URL 段(使用单组括号)的文件，其值将以字符串形式返回：

```js
---
filename: functions/users/[user].js
---
export function onRequest(context) {
  return new Response(context.params.user)
}
```

对于向 `/users/daniel` 提出的请求，上述逻辑将返回 `daniel`。


对于与多个 URL 段(使用双组括号)匹配的文件，其值将以数组形式返回：

```js
---
filename: functions/users/[[catchall]].js
---
export function onRequest(context) {
  return new Response(JSON.stringify(context.params.catchall))
}
```

对于向 `/users/daniel/xyz/123` 的请求，上述逻辑将返回 `["daniel", "xyz", "123"]`。

## 函数调用路线

在纯静态项目中，Pages 提供无限制的免费请求。但是，一旦在 Pages 项目中添加了函数，所有请求都将默认调用你的函数。要继续接收无限制的免费静态请求，请通过创建一个 `_routes.json` 文件来排除项目的静态路由。使用 Pages CI 或 Wrangler 发布项目时，如果在项目中检测到 `functions` 目录，该文件就会自动生成。

{{<Aside type="note">}}

有些框架(如 Remix、SvelteKit)还会自动生成一个 `_routes.json` 文件。不过，如果你喜欢的框架不会这样做，请在它们的框架仓库中创建一个问题，并链接到本页面，或者在 [Discord](https://discord.cloudflare.com) 上告诉我们。有关全栈框架的更多信息，请参阅[框架指南](/pages/framework-guides/)。

{{</Aside>}}

### 创建一个 `_routes.json` 文件

创建一个 `_routes.json`文件，以控制函数何时被调用。该文件应放在项目的输出目录中。

该文件将包括三个不同的属性：

* **version**：定义模式的版本。目前模式只有一个版本(版本 1)，但我们可能会在未来添加更多版本，并力求向后兼容。
* **include**：定义函数将调用的路由。接受通配符行为。
* **exclude**：定义函数不会调用的路由。接受通配符行为。`exclude`总是比 `include`优先。

{{<Aside type="note">}}

通配符可匹配任意数量的路径段(斜线)。例如，`/users/*`将匹配`/users/`路径之后的所有内容。

{{</Aside>}}

#### 配置示例

下面是 `_routes.json`的示例。

```json
---
filename: _routes.json
---
{
   "version": 1,
   "include": ["/*"],
   "exclude": []
}
```

该 `_routes.json`将在所有路由上调用你的 Functions。

下面是另一个 `_routes.json` 文件示例。任何位于 `/build` 目录内的路由都不会调用函数，也不会产生函数调用费用。

```json
---
filename: _routes.json
---
{
   "version": 1,
   "include": ["/*"],
   "exclude": ["/build/*"]
}
```

#### 限制

函数调用路由有以下限制：

* 必须至少有一条包含规则。
* 包含/排除规则合计不得超过 100 条。
* 每条规则不得超过 100 个字符。