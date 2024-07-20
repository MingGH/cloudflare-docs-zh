---
type: example
summary: A Pages Functions for appending CORS headers.
tags:
  - CORS
pcx_content_type: configuration
title: 添加Adding CORS headers
weight: 1002
layout: example
---

本示例是 Cloudflare Pages模板软件仓库中的一个片段。

```ts
---
filename: /functions/_middleware.ts
---

// Respond to OPTIONS method
export const onRequestOptions: PagesFunction = async () => {
  return new Response(null, {
    status: 204,
    headers: {
      'Access-Control-Allow-Origin': '*',
      'Access-Control-Allow-Headers': '*',
      'Access-Control-Allow-Methods': 'GET, OPTIONS',
      'Access-Control-Max-Age': '86400',
    },
  });
};

// Set CORS to all /api responses
export const onRequest: PagesFunction = async (context) => {
  const response = await context.next();
  response.headers.set('Access-Control-Allow-Origin', '*');
  response.headers.set('Access-Control-Max-Age', '86400');
  return response;
};

```