---
updated: 2023-05-31
pcx_content_type: tutorial
content_type: 📝 Tutorial
difficulty: Intermediate
title: Build an API for your front end using Pages Functions
---

# 使用Pages Functions为前端创建应用程序接口

{{<tutorial-date-info>}}

在本教程中，你将构建一个全栈 Pages 应用程序。你的应用程序将包含

* 使用 Cloudflare 页面和 [React 框架](/pages/framework-guides/deploy-a-react-site/)构建的前端。
* 使用 [Pages Functions](/pages/functions/get-started/)构建的 JSON API 可返回博客文章，并可在前端进行检索和渲染。

如果你希望使用无头内容管理系统(headless CMS)而不是应用程序接口(API)来呈现博客内容，请参阅[无头内容管理系统教程](/pages/tutorials/build-a-blog-using-nuxt-and-sanity/)。

## 建立你的前端

首先，使用 React 框架创建一个新的 Pages 应用程序。

### 创建一个新的 React 项目

在终端中使用 `create-react-app` 命令创建一个名为 `blog-frontend` 的新 React 项目。进入新创建的 `blog-frontend` 目录并启动本地开发服务器：

```sh
---
header: Create a new React application
---
$ npx create-react-app blog-frontend
$ cd blog-frontend
$ npm start
```

### 设置你的 React 项目

设置 React 项目

1. 在 `blog-frontend` 目录根目录下安装 [React Router](https://reactrouter.com/en/main/start/tutorial)。

使用 `npm`：

```sh
$ npm install react-router-dom@6
```
With `yarn`:

```sh
$ yarn add react-router-dom@6
```

2. 清除 `src/App.js` 中的内容。复制并粘贴以下代码，将 React 路由器导入 `App.js`，并新建一个包含两条路由的路由器：

```js
---
filename: src/App.js
---
import { Routes, Route } from 'react-router-dom';

import Posts from './components/posts';
import Post from './components/post';

function App() {
  return (
    <Routes>
      <Route path="/" element={<Posts />} />
      <Route path="/posts/:id" element={<Post />} />
    </Routes>
  );
}

export default App;
```

3. 在 `src` 目录中新建一个名为 `components` 的文件夹。
4. 在 `components` 目录中创建两个文件：`posts.js`和 `post.js`。这些文件将从 API 中加载博客文章，并对其进行渲染。
5. 用以下代码填充 `posts.js`：

```js
---
filename: src/components/posts.js
---
import React, { useEffect, useState } from 'react';
import { Link } from "react-router-dom";

const Posts = () => {
    const [posts, setPosts] = useState([]);

    useEffect(() => {
        const getPosts = async () => {
            const resp = await fetch('/api/posts');
            const postsResp = await resp.json();
            setPosts(postsResp);
        };

        getPosts();
    }, []);

    return (
        <div>
            <h1>Posts</h1>
            {posts.map(post => (
                <div key={post.id}>
                    <h2>
                        <Link to={`/posts/${post.id}`}>{post.title}</Link>
                    </h2>
                </div>
            ))}
        </div>
    );
};

export default Posts;
```

6. 用以下代码填充 `post.js`：

```js
---
filename: src/components/post.js
---
import React, { useEffect, useState } from 'react';
import { Link, useParams } from 'react-router-dom';

const Post = () => {
    const [post, setPost] = useState({});
    const { id } = useParams();

    useEffect(() => {
        const getPost = async () => {
            const resp = await fetch(`/api/post/${id}`);
            const postResp = await resp.json();
            setPost(postResp);
        };

        getPost();
    }, [id]);

    if (!Object.keys(post).length) return <div />;

    return (
        <div>
            <h1>{post.title}</h1>
            <p>{post.text}</p>
            <p>
                <em>Published {new Date(post.published_at).toLocaleString()}</em>
            </p>
            <p>
                <Link to="/">Go back</Link>
            </p>
        </div>
    );
};

export default Post;
```

## 建立你的应用程序接口

现在，你将创建一个 Pages Function，用于存储博客内容并通过 JSON API 进行检索。

### 撰写你的Pages Function

创建作为 JSON API 的Pages Function：

1. 在 `blog-frontend` 目录下创建 `functions`目录。
2. 在 `functions` 中创建名为 `api` 的目录。
3. 在 `api` 目录下创建一个 `posts.js` 文件。
4. 用以下代码填充 `posts.js`：

```js
---
filename: functions/api/posts.js
---
import posts from './post/data'

export function onRequestGet() {
    return Response.json(posts)
}
```

这段代码将获取博客数据(来自 `data.js`，你将在第 8 步中制作)，并从路径 `/api/posts`以 JSON 响应形式返回。

5. 在 `api` 目录中创建名为 `post` 的目录。
6. 在 `post` 目录中创建一个 `data.js` 文件。
7. 用以下代码填充 `data.js`。这是你的博客内容、博客标题和其他博客信息的存放位置。

```js
---
filename: functions/api/post/data.js
---
const posts = [
    {
        id: 1,
        title: 'My first blog post',
        text: 'Hello world! This is my first blog post on my new Cloudflare Workers + Pages blog.',
        published_at: new Date('2020-10-23'),
    },
    {
        id: 2,
        title: 'Updating my blog',
        text: "It's my second blog post! I'm still writing and publishing using Cloudflare Workers + Pages :)",
        published_at: new Date('2020-10-26'),
    },
];

export default posts
```
8. 在 `post` 目录下创建一个`[[id]].js`文件。
9. 用以下代码填充 `[[id]].js`：

```js
---
filename: [[id]].js
---
import posts from './data'

export function onRequestGet(context) {
    const id = context.params.id

    if (!id) {
        return new Response('Not found', { status: 404 })
    }

    const post = posts.find(post => post.id === Number(id))

    if (!post) {
        return new Response('Not found', { status: 404 })
    }

    return Response.json(post)
}
```

`[[id]].js`是一个[动态路由](/pages/functions/routing#dynamic-routes)，用于接受博文`id`。

## 部署

配置好 Pages 应用程序和 Pages 功能后，使用 Wrangler 或通过仪表板部署项目。

### 使用 Wrangler 进行部署

在 `blog-frontend` 目录中，运行 [`wrangler pages deploy`](/workers/wrangler/commands/#deploy-1) 将项目部署到 Cloudflare 控制面板。

```sh
$ wrangler pages deploy blog-frontend
```

### 通过仪表板部署

要通过 Cloudflare 控制面板进行部署，你需要为 Pages 项目创建一个新的 Git 仓库，并将 Git 仓库连接到 Cloudflare。本教程使用 GitHub 作为 Git 提供商。

#### 创建新的存储库

访问 [repo.new](https://repo.new) 创建一个新的 GitHub 仓库。创建新仓库后，在终端运行以下命令，准备并向 GitHub 推送本地应用程序：

```sh
$ git init
$ git remote add origin https://github.com/<YOUR-GH-USERNAME>/<REPOSITORY-NAME>
$ git add .
$ git commit -m "Initial commit"
$ git branch -M main
$ git push -u origin main
```

#### 使用 Cloudflare 页面进行部署

将应用程序部署到 Pages：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 "**设置构建和部署**"部分提供以下信息：

<div>

| Configuration option | Value           |
| -------------------- | --------------- |
| Production branch    | `main`          |
| Build command        | `npm run build` |
| Build directory      | `build`         |

</div>

配置网站后，开始首次部署。你应该会看到 Cloudflare 页面正在安装 `blog-frontend`、你的项目依赖项，并正在构建你的网站。

完成本教程后，你就创建了一个全栈 Pages 应用程序。

## 相关资源

* 了解[页面功能路由](/pages/functions/routing)