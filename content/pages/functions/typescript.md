---
pcx_content_type: how-to
title: TypeScript
weight: 8
---

# TypeScript

Pages 函数支持 TypeScript。在你的 `/functions` 目录中编写任何文件时，请使用 `.ts` 扩展名而不是 `.js` 扩展名，以便开始使用 TypeScript。

要将运行时类型添加到项目中，请运行

```sh
$ npm install --save-dev typescript @cloudflare/workers-types
```

然后创建一个 `functions/tsconfig.json` 文件来配置运行时类型：

```json
---
filename: functions/tsconfig.json
---
{
  "compilerOptions": {
    "target": "esnext",
    "module": "esnext",
    "lib": ["esnext"],
    "types": ["@cloudflare/workers-types"]
  }
}
```

如果项目根目录中已经有了 `tsconfig.json`，则可能需要明确排除 `/functions`目录，以避免冲突。排除 `/functions` 目录：

```json
---
filename: tsconfig.json
---
{
  "include": ["src/**/*"],
  "exclude": ["functions/**/*"],
  "compilerOptions": {

  }
}
```

页面函数可使用 `PagesFunction` 类型键入。该类型接受一个 `Env` 参数。要使用 `env` 参数，请执行以下操作

```ts
---
filename: functions/example.ts
---
interface Env {
	KV: KVNamespace;
}

export const onRequest: PagesFunction<Env> = async (context) => {
	const value = await context.env.KV.get('example');
 	return new Response(value);
}
```