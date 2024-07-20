---
pcx_content_type: how-to
title: Sphinx
---

# Sphinx

[Sphinx](https://www.sphinx-doc.org/)是一个可以轻松创建文档的工具，最初是为发布 Python 文档而设计的。它以简单易用而著称。

在本指南中，你将创建一个新的 Sphinx 项目并使用 Cloudflare Pages 进行部署。

## 先决条件

- Python 3 - Sphinx 基于 Python，因此必须安装 Python

- [pip](https://pypi.org/project/pip/) - PyPA 推荐的安装 Python 软件包的工具

- [pipenv](https://pipenv.pypa.io/en/latest/) - 自动为项目创建和管理虚拟环境

{{<Aside type="note">}}

如果你已经在运行 Python 3.7 版本，请确保在开始本指南之前，你的计算机上也安装了 Python 3.7 版本。Python 3.7 是 Cloudflare Pages 支持的最新版本。

{{</Aside>}}

Python 3.7 的最新版本是 3.7.11：

[Python 3.7.11](https://www.python.org/downloads/release/python-3711/)

### 安装 Python

有关安装指导，请参阅 Python 官方文档：

- [Windows](https://www.python.org/downloads/windows/)
- [Linux/UNIX](https://www.python.org/downloads/source/)
- [macOS](https://www.python.org/downloads/macos/)
- [其他](https://www.python.org/download/other/)

### 安装 Pipenv

如果你在安装 3.7 版之前已经安装了较早版本的 Python，那么你可能已经安装的其他全局包可能会干扰下面安装 Pipenv 的步骤，或你的其他依赖于全局包的 Python 项目。

[Pipenv](https://pipenv.pypa.io/en/latest/)是一个基于 Python 的软件包管理器，可让虚拟环境管理变得简单。本指南不要求你事先具备使用 Pipenv 的经验或知识以完成 Sphinx 站点部署。Cloudflare Pages 本身支持使用 Pipenv，默认情况下已安装最新版本。

安装 Pipenv 的最快方法是运行命令：

```shell
$ pip install --user pipenv
```

该命令将把 Pipenv 安装到用户层目录，并可通过终端访问。运行以下命令并查看预期输出即可确认：

```shell
$ pipenv --version
pipenv, version 2021.5.29
```

### 创建 Sphinx 项目目录

在终端运行以下命令创建新目录并导航到该目录：

```shell
$ mkdir my-wonderful-new-sphinx-project
$ cd my-wonderful-new-sphinx-project
```

### 使用 Python 3.7 的 Pipenv

Pipenv 允许你指定与虚拟环境相关联的 Python 版本。在本指南中，Sphinx 项目的虚拟环境必须使用 Python 3.7。

使用以下命令

```shell
~my-wonderful-new-sphinx-project$ pipenv --python 3.7
```

输出结果如下

```bash
Creating a virtualenv for this project...
Pipfile: /home/ubuntu/my-wonderful-new-sphinx-project/Pipfile
Using /usr/bin/python3.7m (3.7.11) to create virtualenv...
⠸ Creating virtual environment...created virtual environment CPython3.7.11.final.0-64 in 1598ms
  creator CPython3Posix(dest=/home/ubuntu/.local/share/virtualenvs/my-wonderful-new-sphinx-project-Y2HfWoOr, clear=False, no_vcs_ignore=False, global=False)
  seeder FromAppData(download=False, pip=bundle, setuptools=bundle, wheel=bundle, via=copy, app_data_dir=/home/ubuntu/.local/share/virtualenv)
    added seed packages: pip==21.1.3, setuptools==57.1.0, wheel==0.36.2
  activators BashActivator,CShellActivator,FishActivator,PowerShellActivator,PythonActivator,XonshActivator

✔ Successfully created virtual environment!
Virtualenv location: /home/ubuntu/.local/share/virtualenvs/my-wonderful-new-sphinx-project-Y2HfWoOr
Creating a Pipfile for this project...
```

列出目录内容：

```shell
~my-wonderful-new-sphinx-project$ ls
Pipfile
```

### 安装Sphinx

安装 Sphinx 之前，请创建你希望项目所在的目录。

在终端运行以下命令安装 Sphinx：

```shell
~/my-wonderful-new-sphinx-project$ pipenv install sphinx
```

你应该会看到类似下面的输出：

```bash
Installing sphinx...
Adding sphinx to Pipfile's [packages]...
✔ Installation Succeeded
Pipfile.lock not found, creating...
Locking [dev-packages] dependencies...
Locking [packages] dependencies...
Building requirements...
Resolving dependencies...
✔ Success!
Updated Pipfile.lock (763aa3)!
Installing dependencies from Pipfile.lock (763aa3)...
  🐍   ▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉▉ 0/0 — 00:00:00
To activate this project's virtualenv, run pipenv shell.
Alternatively, run a command inside the virtualenv with pipenv run.
```

这将把 Sphinx 安装到由 Pipenv 管理的新虚拟环境中。你应该看到这样的目录结构：

```bash
my-wonderful-new-sphinx-project
|--Pipfile
|--Pipfile.lock
```

## 创建一个新项目

安装好 Sphinx 后，现在就可以运行 quickstart 命令，为自己创建一个模板项目。该命令只能在上一步创建的 Pipenv 环境中运行。要进入该环境，请在终端运行以下命令：

```shell
~/my-wonderful-new-sphinx-project$ pipenv shell
Launching subshell in virtual environment...
ubuntu@sphinx-demo:~/my-wonderful-new-sphinx-project$  . /home/ubuntu/.local/share/virtualenvs/my-wonderful-new-sphinx-project-Y2HfWoOr/bin/activate
```

现在运行以下命令

```shell
(my-wonderful-new-sphinx-project) user@hostname:~/my-wonderful-new-sphinx-project$ sphinx-quickstart
```

你将收到一些问题，请在下面回答：

```shell
$ Separate source and build directories (y/n) [n]: Y
$ Project name: <Your project name>
$ Author name(s): <You Author Name>
$ Project release []: <You can accept default here or provide a version>
$ Project language [en]: <You can accept en here or provide a regional language code>
```

这将在你的活动目录中创建四个新文件：`source/conf.py`、`index.rst`、`Makefile` 和`make.bat`：

```bash
my-wonderful-new-sphinx-project
|--Pipfile
|--Pipfile.lock
|--source
|----_static
|----_templates
|----conf.py
|----index.rst
|--Makefile
|--make.bat
```

现在，你已经拥有了开始将网站部署到 Cloudflare 页面所需的一切。如需了解如何使用 Sphinx 创建文档，请参阅官方[Sphinx 文档](https://www.sphinx-doc.org/en/master/usage/quickstart.html)。

{{<render file="_tutorials-before-you-start.md">}}

## 创建 GitHub 仓库

<! -- 待办事项：检查，我们是否需要在这里指定所有各种 ssh + 配置？
     (如果没有，则应使用 _create-github-repository 部分)-->

在 pipenv shell 会话之外的另一个终端窗口中，验证基于 SSH 密钥的身份验证是否正常：

```shell
$ eval "$(ssh-agent)"
$ ssh-add -T ~/.ssh/id_rsa.pub
$ ssh -T git@github.com

The authenticity of host 'github.com (140.82.113.4)' can't be established.
RSA key fingerprint is SHA256:nThbg6kXUpJWGl7E1IGOCspRomTxdCARLviKw6E5SY8.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added 'github.com,140.82.113.4' (RSA) to the list of known hosts.
Hi yourgithubusername! You've successfully authenticated, but GitHub does not provide shell access.
```

访问 [repo.new](https://repo.new)，创建一个新的 GitHub 仓库。仓库建立后，在终端运行以下命令，将应用程序推送到 GitHub：

```shell
$ git init
$ git config user.name "Your Name"
$ git config user.email "username@domain.com"
$ git remote add origin git@github.com:yourgithubusername/githubrepo.git
$ git add .
$ git commit -m "Initial commit"
$ git branch -M main
$ git push -u origin main
```

## 使用 Cloudflare 页面部署

将网站部署到页面：

1. 登录 [Cloudflare 仪表板](https://dash.cloudflare.com/) 并选择你的账户。
2. 在账户主页，选择 **工作者和页面**> **创建应用程序**> **页面**> **连接到 Git**。
3. 选择创建的新 GitHub 仓库，并在 `**设置构建和部署**`部分提供以下信息：

<div>

| Configuration option | Value        |
| -------------------- | ------------ |
| Production branch    | `main`       |
| Build command        | `make html`  |
| Build directory      | `build/html` |

</div>

在配置下方，请确保设置用于指定 `PYTHON_VERSION` 的环境变量。

例如

<div>

| Variable name  | Value |
| -------------- | ----- |
| PYTHON_VERSION | 3.7   |

</div>

配置网站后，你可以开始首次部署。在部署之前，你应该看到 Cloudflare 页面正在安装 `Pipenv`、项目依赖项并构建网站。

{{<Aside type="note">}}

有关将你的第一个网站部署到 Cloudflare Pages 的完整指南，请参阅 [入门指南](/pages/get-started/)。

{{</Aside>}}

部署网站后，你将在 `*.pages.dev`上为你的项目获得一个唯一的子域。每次你向 Sphinx 网站提交新代码时，Cloudflare Pages 都会自动重建你的项目并进行部署。

你还可以访问新的拉取请求上的 [预览部署](/pages/configuration/preview-deployments/)，这样你就可以在将更改部署到生产环境之前预览网站的外观。

{{<render file="/_framework-guides/_learn-more.md" withParameters="Sphinx">}}