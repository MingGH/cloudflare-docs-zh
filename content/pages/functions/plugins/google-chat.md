---
pcx_content_type: concept
title: Google Chat
weight: 1
---

# 谷歌聊天页面插件

谷歌聊天页面插件可创建一个能回复消息的谷歌聊天机器人。它还包含一个 API，用于与谷歌聊天进行交互(例如创建消息)，而无需用户输入。该 API 在警告等情况下非常有用。

## 安装

```sh
$ npm install @cloudflare/pages-plugin-google-chat
```

## 使用方法

```typescript
---
filename: functions/google-chat.ts
---
import googleChatPlugin from "@cloudflare/pages-plugin-google-chat";

export const onRequest: PagesFunction = googleChatPlugin(async (message) => {
  if (message.text.includes("ping")) {
    return { text: "pong" };
  }

  return { text: "Sorry, I could not understand your message." };
});
```

该插件接收一个函数，该函数反过来接收一个传入消息，并返回一个响应消息的 `Promise`(如果没有任何响应，则返回 `void`)。

该插件只公开一个路由，即创建机器人时应在 Google 云控制台中设置的 URL。

![Google 云控制台的 Google Chat API 连接设置显示已选择 `App URL`，并在 `App URL`文本输入中输入了 `https://example.com/google-chat`。](/images/pages/platform/functions/google-chat.png)

### API

可以使用 `GoogleChatAPI` 类直接调用 Google 聊天 API：

```typescript
---
filename: functions/alert.ts
---
import { GoogleChatAPI } from "@cloudflare/pages-plugin-google-chat/api";

export const onRequest: PagesFunction = () => {
  // Initialize a GoogleChatAPI with your service account's credentials
  const googleChat = new GoogleChatAPI({
    credentials: {
      client_email: "SERVICE_ACCOUNT_EMAIL_ADDRESS",
      private_key: "SERVICE_ACCOUNT_PRIVATE_KEY",
    },
  });

  // Post a message
  // https://developers.google.com/chat/api/reference/rest/v1/spaces.messages/create
  const message = await googleChat.createMessage(
    { parent: "spaces/AAAAAAAAAAA" },
    undefined,
    {
      text: "I'm an alert!",
    }
  );

  return new Response("Alert sent.");
};
```

我们建议将服务账户的凭据存储在 KV 中，而不是上述的纯文本中。

以下函数可用于 `GoogleChatAPI` 实例。如 [Google Chat API 文档](https://developers.google.com/chat/api/reference/rest) 所述，每个函数最多包含三个参数：路径参数对象、查询参数对象和请求正文对象。

- [`downloadMedia`](https://developers.google.com/chat/api/reference/rest/v1/media/download)
- [`getSpace`](https://developers.google.com/chat/api/reference/rest/v1/spaces/get)
- [`listSpaces`](https://developers.google.com/chat/api/reference/rest/v1/spaces/list)
- [`getMember`](https://developers.google.com/chat/api/reference/rest/v1/spaces.members/get)
- [`listMembers`](https://developers.google.com/chat/api/reference/rest/v1/spaces.members/list)
- [`createMessage`](https://developers.google.com/chat/api/reference/rest/v1/spaces.messages/create)
- [`deleteMessage`](https://developers.google.com/chat/api/reference/rest/v1/spaces.messages/delete)
- [`getMessage`](https://developers.google.com/chat/api/reference/rest/v1/spaces.messages/get)
- [`updateMessage`](https://developers.google.com/chat/api/reference/rest/v1/spaces.messages/update)
- [`getAttachment`](https://developers.google.com/chat/api/reference/rest/v1/spaces.messages.attachments/get)