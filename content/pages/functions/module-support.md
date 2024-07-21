---
pcx_content_type: reference
title: 模块支持
weight: 13
---

# 模块支持

Pages Functions 支持多种模块类型，就像 [Workers](https://blog.cloudflare.com/workers-javascript-modules/)。这意味着你可以在 Functions 代码中导入和使用 WebAssembly (Wasm)、`text` 和`binary` 文件等外部模块。

本指南将指导你如何在 Pages 函数中使用这些不同的模块类型。

## ECMAScript 模块

ECMAScript 模块(简称 ES 模块)是 JavaScript 的官方[标准化](https://tc39.es/ecma262/#sec-modules) 模块系统。它是编写模块化和可重用 JavaScript 代码的推荐机制。

[ES 模块](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide/Modules) 是通过使用 `import` 和 `export` 语句来定义的。下面是一个以 ES 模块格式编写的脚本和导入该模块的 Pages 函数的示例：

```js
---
filename: src/greeting.ts
---
export function greeting(name: string): string {
  return `Hello ${name}!`;
}
```

```js
---
filename: functions/hello.ts
---
import { greeting } from "../src/greeting.ts";

export async function onRequest(context) {
    return new Response(`${greeting("Pages Functions")}`);
}
```

## WebAssembly 模块

[WebAssembly](/workers/runtime-apis/webassembly/)(缩写为 Wasm)允许你将 Rust、Go 或 C 等语言编译成二进制格式，以便在各种环境中运行，包括网络浏览器、Cloudflare Workers、Cloudflare Pages Functions 和其他 WebAssembly 运行时。

WebAssembly 中可分发、可加载、可执行的代码单元称为 [模块](https://webassembly.github.io/spec/core/syntax/modules.html)。

以下是如何在页面功能代码中导入 Wasm 模块的基本示例：

```js
---
filename: functions/meaning-of-life.ts
---
import addModule from "add.wasm";

export async function onRequest() {
	const addInstance = await WebAssembly.instantiate(addModule);
	return new Response(
		`The meaning of life is ${addInstance.exports.add(20, 1)}`
	);
}
```

## 文本模块

文本模块是一种以 `String `形式导入 HTML 文件等资源的非标准化方法。

将以下 HTML 文件导入到你的页面功能代码中：

```html
---
filename: index.html
---
<!DOCTYPE html>
<html>
  <body>
    <h1>Hello Pages Functions!</h1>
  </body>
</html>
```

使用以下脚本：

```js
---
filename: functions/hey.ts
---
import html from "../index.html";

export async function onRequest() {
	return new Response(
		html,
    {
      headers: { "Content-Type": "text/html" }
    }
	);
}
```

## 二进制模块

二进制模块是一种非标准化的导入二进制数据(如图像)的方式，它以 `ArrayBuffer`(数组缓冲区)的形式导入。

下面是一个基本示例，说明如何在 Pages 函数代码中导入二进制文件中的数据：

```js
---
filename: functions/data.ts
---
import data from "../my-data.bin";

export async function onRequest() {
	return new Response(
		data,
    {
      headers: { "Content-Type": "application/octet-stream" }
    }
	);
}
```