---
title: 使用 GitHub 操作自动部署 WordPress 插件和主题
date: 2023-10-25 19:28:00
categories:
  - 教程指南
tags:
  - WordPress
  - GitHub
description: 这是一篇介绍如何使用 GitHub Actions 自动部署 WordPress 插件和主题的教程文章,整体来说,这是一篇通俗易懂的 WordPress 自动部署教程,对需要使用 GitHub 集成来简化部署流程的人会有参考价值。
cover: https://th.bing.com/th/id/R.e485e17f76a81acd9f843f5430f4b9e1?rik=dfxG5FWOA7qqtQ&pid=ImgRaw&r=0
---

## 引言

在 WordPress 开发和项目协作中,使用 GitHub 进行集成可以在许多方面提供帮助。其中之一是通过自动部署的方式进行部署,而不是通过 FTP 进行手动上传。这种集成可以极大地节省时间,特别是在使用自定义主题和插件时更为重要。本文将介绍如何设置 WordPress GitHub 集成,以便管理 WordPress 主题和插件的部署。

在本文中,我们将不涉及以下内容:

- WordPress 核心文件:我们不应该编辑 WordPress 核心文件,因此将它们包含在我们的代码库中没有意义。

- 数据库:尝试对 WordPress 数据库进行版本控制会引发一系列问题,因此我们不会涉及此内容。 

- WordPress 插件和主题的开发:我们假设您已经完成了插件和主题的开发工作,并准备进行部署。

接下来,让我们开始介绍如何使用样例工作流来同步我们的 WordPress 主题和插件!

## 部署 WordPress 主题

以下是用于将您编写的主题文件与服务器上的 `/wp-content/themes` 文件夹同步的 .yml 代码。对于像 Digital Ocean 这样的 LAMP 服务器,这是一个示例工作流程:

```yaml
name: Deploy Theme

on:
  push: 
    branches: [master]

env:
  SSH_USER: ${{ secrets.SSH_USER }}
  SSH_HOST: ${{ secrets.SSH_HOST }}
  
jobs:
  deploy:
    name: Deploy WordPress Theme on Digital Ocean  
    runs-on: ubuntu-latest

    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Set SSH Connection
        run: |
          mkdir -p ~/.ssh/
          echo "$SSH_KEY" > ~/.ssh/deploy.key
          chmod 600 ~/.ssh/deploy.key
          cat >>~/.ssh/config <<END
          Host digitalocean
            HostName $SSH_HOST
            User $SSH_USER
            IdentityFile ~/.ssh/deploy.key
            StrictHostKeyChecking no
          END
        env:
          SSH_KEY: ${{ secrets.DEPLOY_KEY }}
          
      - name: Sync theme files
        run: |
          rsync --delete -avO \
            --exclude /deploy_key \
            --exclude /.git/ \
            --exclude /.github/ \
            ./ ${{ env.SSH_USER }}@${{ env.SSH_HOST }}:${{ env.DEST }}
        env:
          SSH_HOST: digitalocean
          DEST: "/var/www/your-domain/wp-content/themes/theme-folder"
```

**工作流名称**

首行中的 `name` 字段指定了工作流的名称,这个名称可以自定义为描述该工作流的目的。

**触发器设置**

接下来,我们定义了触发器,即当我们将主题的提交推送到存储库的 `master` 分支时,工作流将被触发。您可以根据需要自定义触发器设置,详细信息请参阅 GitHub 文档。

```yaml
on:
  push:
    branches: [master]
```

**密钥设置**

我们创建了用于自定义密钥的变量。这些变量可以存储在 GitHub 的 Secrets 中,供工作流程使用。

```yaml
env:
  SSH_USER: ${{ secrets.SSH_USER }}
  SSH_HOST: ${{ secrets.SSH_HOST }}
```

**工作** 

在 GitHub Actions 的工作流文件中,jobs 部分定义了一个或多个要在工作流触发时执行的作业。

```yaml
jobs:
  deploy:
    name: Deploy WordPress Theme on Digital Ocean
    runs-on: ubuntu-latest
```

在上述示例中:

- `deploy` 是作业的名称,可以自定义以描述该作业的目的。
- `name` 是一个可选字段,用于在查看工作流时使其更易理解。 
- `runs-on` 指定作业将在其上执行的虚拟环境或 Runner 的类型。在此示例中,使用了 `ubuntu-latest` Runner,表示作业将在最新可用的 Ubuntu 操作系统版本上运行。

**步骤**

GitHub Actions 的工作流文件中,steps 部分定义了作业中要执行的一系列单独任务。

```yaml
steps:
  - name: Checkout 
    uses: actions/checkout@v2
```

在上述示例中:

- `steps` 包含一个按指定顺序执行的操作列表。
- `name` 是一个可选字段,用于在查看工作流时使其更易理解。
- `uses` 指定要执行的操作。在此示例中,使用 `actions/checkout@v2` 操作来检出源代码存储库。

**设置 SSH 连接**

这部分是工作流中负责设置 SSH 连接以实现对远程主机的安全访问的部分。

```yaml
- name: Set SSH Connection
  run: |
    mkdir -p ~/.ssh/
    echo "$SSH_KEY" > ~/.ssh/deploy.key
    chmod 600 ~/.ssh/deploy.key
    cat >>~/.ssh/config <<END
    Host digitalocean
      HostName $SSH_HOST
      User $SSH_USER
      IdentityFile ~/.ssh/deploy.key
      StrictHostKeyChecking no
    END
  env:
    SSH_KEY: ${{ secrets.DEPLOY_KEY }}
```

在上述示例中:

- `name` 是一个可选字段,用于在前面的步骤中更好地理解该步骤的作用。
- `run` 定义了要执行的步骤或 shell 命令。在此示例中:
  - 如果 `~/.ssh/` 目录不存在,则创建该目录。
  - 将存储在 `SSH_KEY` 环境变量中的 SSH 密钥写入 `~/.ssh/deploy.key` 文件。
  - 设置 `~/.ssh/deploy.key` 的权限,以确保只有用户可以访问该文件。 
  - 向 `~/.ssh/config` 文件追加 SSH 配置,包括主机名、用户名和 SSH 密钥的路径。此外,它禁用了对 "digitalocean" 主机的严格主机密钥检查。
- `env` 部分指定了工作流中使用的环境变量,在此示例中,它定义了 `SSH_KEY` 变量,并从 GitHub 的 Secrets 中获取其值。

**同步主题文件**

这个部分负责使用 rsync 命令将主题文件同步到远程服务器。

```yaml
- name: Sync theme files
  run: |  
    rsync --delete -avO \
      --exclude /deploy_key \
      --exclude /.git/ \
      --exclude /.github/ \
      ./ ${{ env.SSH_USER }}@${{ env.SSH_HOST }}:${{ env.DEST }}
  env:
    SSH_HOST: digitalocean
    DEST: "/var/www/your-domain/wp-content/themes/theme-folder"
```

rsync 命令执行以下任务:

- `--delete`:确保在远程服务器上也删除本地已删除的文件。
- `-avO`:为归档模式、详细输出和优化设置 `rsync` 选项。
- `--exclude`:指定要从同步中排除的文件或目录。在此示例中,它排除了 `/deploy_key`、`/.git/` 和 `/.github/` 文件夹,但您可以根据项目的需要进行自定义设置。 
- `./`:指定要从中同步的源目录,即工作流的当前目录。
- `${{ env.SSH_USER }}@${{ env.SSH_HOST }}:${{ env.DEST }}`:指定同步的目标。它使用环境变量来设置 SSH 用户名、主机和目标路径。

**变量定义**

这一部分包含了工作流中使用的其他环境变量。

```yaml
env:
  SSH_HOST: digitalocean
  DEST: "/var/www/your-domain/wp-content/themes/theme-folder"
```

## 部署 WordPress 插件

要部署插件,您可以按照上述示例中的相同思路进行操作,只需将部署路径更改为 `/var/www/your-domain/wp-content/plugins/plugin-folder`。以下是一个示例的工作流程:

```yaml
name: Deploy Plugin

on: 
  push:
    branches: [master]

env:
  SSH_USER: ${{ secrets.SSH_USER }}
  SSH_HOST: ${{ secrets.SSH_HOST }}
  
jobs:
  deploy:  
    name: Deploy WordPress Plugin on Digital Ocean
    runs-on: ubuntu-20.04

    steps:
      - name: Checkout
        uses: actions/checkout@v2

      - name: Set SSH Connection  
        run: |
          mkdir -p ~/.ssh/
          echo "$SSH_KEY" > ~/.ssh/deploy.key
          chmod 600 ~/.ssh/deploy.key
          cat >>~/.ssh/config <<END
          Host digitalocean
            HostName $SSH_HOST
            User $SSH_USER
            IdentityFile ~/.ssh/deploy.key
            StrictHostKeyChecking no
          END
        env:
          SSH_KEY: ${{ secrets.DEPLOY_KEY }}
          
      - name: Sync plugin files
        run: |
          rsync --delete -avO \
            --exclude /deploy_key \
            --exclude /.git/ \
            --exclude /.github/ \
            ./ ${{ env.SSH_USER }}@${{ env.SSH_HOST }}:${{ env.DEST }}
        env:
          SSH_HOST: digitalocean
          DEST: "/var/www/your-domain/wp-content/plugins/plugin-folder"
```

这是一个包含了一些用于不同服务器的自定义工作流程的样例存储库。欢迎通过提交 pull request 添加更多工作流程或提出对当前工作流程的改进建议。如果对您有用,请考虑给存储库点个赞。

## 结论

本文介绍了如何使用 GitHub 操作自动部署 WordPress 插件和主题。通过将代码存储在 GitHub 仓库中并使用 GitHub Actions 的工作流程,您可以轻松地同步和部署您的 WordPress 插件和主题。这种集成可以显著提高开发效率,减少手动操作的时间和错误。

