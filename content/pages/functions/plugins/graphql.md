---
pcx_content_type: concept
title: GraphQL
weight: 1
---

# GraphQL 页面插件

GraphQL Pages Plugin 可创建一个 GraphQL 服务器，用于响应 `application/json` 和 `application/graphql` `POST` 请求。它使用[the GraphQL Playground](https://github.com/graphql/graphql-playground)响应`GET`请求。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-graphql
```

## 使用方法

```typescript
---
filename: functions/graphql.ts
---
import graphQLPlugin from "@cloudflare/pages-plugin-graphql";
import {
  graphql,
  GraphQLSchema,
  GraphQLObjectType,
  GraphQLString,
} from "graphql";

const schema = new GraphQLSchema({
  query: new GraphQLObjectType({
    name: "RootQueryType",
    fields: {
      hello: {
        type: GraphQLString,
        resolve() {
          return "Hello, world!";
        },
      },
    },
  }),
});

export const onRequest: PagesFunction = graphQLPlugin({
  schema,
  graphql,
});
```

该插件只公开一条路由，因此安装在哪里，它就在哪里可用。在上例中，由于它被挂载在 `functions/graphql.ts` 中，服务器将在 Pages 项目的 `/graphql` 中可用。