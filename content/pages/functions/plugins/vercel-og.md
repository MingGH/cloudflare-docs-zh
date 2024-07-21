---
pcx_content_type: reference
title: vercel/og
---

# `@vercel/og` 页面插件

`@vercel/og` 页面插件是一个为网页渲染社交图片的中间件。它还包括一个用于创建任意图像的 API。

顾名思义，它由[`@vercel/og`](https://vercel.com/docs/concepts/functions/edge-functions/og-image-generation)提供支持。该插件及其底层 [Satori](https://github.com/vercel/satori) 库由 Vercel 团队创建。

## 安装

要安装 `@vercel/og` 页面插件，请运行

```sh
$ npm install @cloudflare/pages-plugin-vercel-og
```

## 使用

```typescript
---
filename: functions/blog/_middleware.tsx
---
import React from "react";
import vercelOGPagesPlugin from "@cloudflare/pages-plugin-vercel-og";

interface Props {
	ogTitle: string;
}

export const onRequest = vercelOGPagesPlugin<Props>({
	imagePathSuffix: "/social-image.png",
	component: ({ ogTitle, pathname }) => {
		return <div style={{ display: "flex" }}>{ogTitle}</div>;
	},
	extractors: {
		on: {
			'meta[property="og:title"]': (props) => ({
				element(element) {
					props.ogTitle = element.getAttribute("content");
				},
			}),
		},
	},
	autoInject: {
		openGraph: true,
	},
});
```

该插件接收一个包含六个属性的对象：

- `imagePathSuffix`：生成图片的路径后缀。例如，如果将此插件挂载到 `functions/blog/_middleware.ts` 处，将 `imagePathSuffix` 设置为 `/social-image.png`，并拥有一个 `/blog/hello-world`页面，则图片将在 `/blog/hello-world/social-image.png`处可用。

- `component`：用于渲染图像的 React 组件。默认情况下，React 组件的 `pathname` 属性等于底层网页的路径名(例如，`/blog/hello-world`)，但可以使用 `extractors` 选项提供更多动态属性。

- `extractors`: 可选对象，有两个可选属性：`on`和`onDocument`。这些属性可被设置为一个函数，该函数接收一个对象，并分别返回[`HTMLRewriter` element handler](/workers/runtime-apis/html-rewriter/#element-handlers)或[document handler](/workers/runtime-apis/html-rewriter/#document-handlers)。对象参数可以更改，以便为 React 组件提供更多属性。在上面的示例中，你将使用元素处理程序从网页中提取 `og:title` 元标记，并将其作为 `ogTitle` 属性传递给 React 组件。这是你用来创建动态图片的主要机制，这些图片使用了底层网页中的值。

- `options`：[一个直接提供给 `@vercel/og` 库的可选对象](https://vercel.com/docs/concepts/functions/edge-functions/og-image-generation/og-image-api)。

- `onError`：一个可选函数，用于返回一个`Response`或一个`Response`的承诺。当向 `imagePathSuffix` 提出请求并提供了 `extractors` 但底层网页不是有效的 HTML 时，该函数将被调用。默认返回 `404` 响应。

- `autoInject`：一个可选对象，带有一个可选属性：`openGraph`。如果设置为 `true`，插件将自动在底层网页上设置 `og:image`、`og:image:height `和 `og:image:width `元标记。

### 生成任意图像

使用该插件的 API 生成任意图像，而不仅仅是作为中间件。

例如，下面的代码将生成一张图片，上面写着 `Hello, world!`，该图片可在 `/greet`中找到。

```typescript
---
filename: functions/greet.tsx
---
import React from "react";
import { ImageResponse } from "@cloudflare/pages-plugin-vercel-og/api";

export const onRequest: PagesFunction = async () => {
  return new ImageResponse(
    <div style={{ display: "flex" }}>Hello, world!</div>,
    {
      width: 1200,
      height: 630,
    }
  );
};
```

这与底层 [`@vercel/og`库](https://vercel.com/docs/concepts/functions/edge-functions/og-image-generation/og-image-api) 提供的 API 相同。