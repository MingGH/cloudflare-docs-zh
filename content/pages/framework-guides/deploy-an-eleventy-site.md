---
pcx_content_type: how-to
title: Eleventy
---

# Eleventy

[Eleventy](https://www.11ty.dev/) is a simple static site generator. In this guide, you will create a new Eleventy site and deploy it using Cloudflare Pages. You will be using the `eleventy` CLI to create a new Eleventy site.

## Installing Eleventy

Install the `eleventy` CLI by running the following command in your terminal:

```sh
$ npm install -g @11ty/eleventy
```

## Creating a new project

There are a lot of [starter projects](https://www.11ty.dev/docs/starter/) available on the Eleventy website. As an example, use the `eleventy-base-blog` project by running the following commands in your terminal:

```sh
$ git clone https://github.com/11ty/eleventy-base-blog.git my-blog-name
$ cd my-blog-name
$ npm install
```

{{<render file="_tutorials-before-you-start.md">}}

## Creating a GitHub repository

Create a new GitHub repository by visiting [repo.new](https://repo.new). After creating a new repository, prepare and push your local application to GitHub by running the following command in your terminal:

```sh
$ git remote set-url origin https://github.com/yourgithubusername/githubrepo
$ git branch -M main
$ git push -u origin main
```

## Deploy with Cloudflare Pages

To deploy your site to Pages:

1. Log in to the [Cloudflare dashboard](https://dash.cloudflare.com/) and select your account.
2. In Account Home, select **Workers & Pages** > **Create application** > **Pages** > **Connect to Git**.
3. Select the new GitHub repository that you created and, in the **Set up builds and deployments** section, select _Eleventy_ as your **Framework preset**. Your selection will provide the following information:

{{<pages-build-preset framework="eleventy">}}

{{<Aside type="note">}}

For the complete guide to deploying your first site to Cloudflare Pages, refer to the [Get started guide](/pages/get-started/).

{{</Aside>}}

After deploying your site, you will receive a unique subdomain for your project on `*.pages.dev`.
Every time you commit new code to your Eleventy site, Cloudflare Pages will automatically rebuild your project and deploy it. You will also get access to [preview deployments](/pages/configuration/preview-deployments/) on new pull requests, so you can preview how changes look to your site before deploying them to production.

{{<render file="/_framework-guides/_learn-more.md" withParameters="Eleventy">}}