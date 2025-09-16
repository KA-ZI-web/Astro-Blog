---
title: git 基础操作
published: 2025-04-01
description: "How to use git"
image: "./cover.jpeg"
tags: ["git"]
category: git
draft: false
---

> Cover image source: [Source](https://image.civitai.com/xG1nkqKTMzGDvpLrqFT7WA/208fc754-890d-4adb-9753-2c963332675d/width=2048/01651-1456859105-(colour_1.5),girl,_Blue,yellow,green,cyan,purple,red,pink,_best,8k,UHD,masterpiece,male%20focus,%201boy,gloves,%20ponytail,%20long%20hair,.jpeg)

# **Git 远程仓库学习笔记**
这是一篇从零开始的小白学习 Git 的学习笔记。

# **目录**
* 1. [一、下载 Git](#Git)
* 2. [二、**建立 SSH 密钥**](#SSH)
* 3. [三、**配置本地项目**](#-1)
* 4. [四、**关联远程仓库**](#-1)
* 5. [五、上传项目到远程仓库](#-1)
* 6. [六、**完成**](#-1)


##  1. <a name='Git'></a>一、下载 Git

1. 访问 Git 下载链接：[https://git-scm.com/downloads](https://git-scm.com/downloads)
2. 选择对应的系统和版本进行安装（本文以 Windows 为例）
3. 等待下载完成并安装（安装过程略）


##  2. <a name='SSH'></a>二、**建立 SSH 密钥**

在进行 Git 版本控制时，HTTP 和 SSH 是两种常见的传输协议。本文采用保密性较强的 SSH 协议。

###  2.1. <a name='GitHub'></a>在 GitHub 上配置密钥

1. 打开 GitHub → 点击头像 → 选择 **Settings**
2. 选择 **SSH and GPG keys** → 点击 **New SSH Key**

###  2.2. <a name=''></a>在本地生成密钥

1. 打开 `Git Bash`
2. 进入 `.ssh` 目录：
```bash
cd ~/.ssh
```
3. 生成密钥：
```bash
ssh-keygen -t rsa -b 4096
```
   设置并记住密钥密码
4. 查看并复制公钥：
```bash
cat id_rsa.pub
```
5. 将公钥内容粘贴到 GitHub 的 SSH Key 配置中，点击 **Add SSH Key**


##  3. <a name='-1'></a>三、**配置本地项目**

###  3.1. <a name='Git-1'></a>Git 基本概念

- **工作区**：电脑中可见的目录
- **暂存区**（stage/index）：位于 `.git/index`，暂存修改的文件
- **本地仓库**：工作区中的 `.git` 目录
- **远程仓库**（remote）：托管在远程服务器上的仓库

###  3.2. <a name='-1'></a>配置用户信息

```bash
git config --global user.name "用户名"
git config --global user.email "用户邮箱"
```

###  3.3. <a name='-1'></a>初始化仓库

1. 用 Git Bash 打开项目根目录
2. 初始化 Git 仓库（生成 `.git` 目录）：
```bash
git init
```

###  3.4. <a name='-1'></a>跟踪与提交文件

1. 跟踪文件（添加到暂存区）：
```bash
git add .          # 跟踪所有文件
git add filename   # 跟踪指定文件
```
2. 提交文件（提交到本地仓库）：
```bash
git commit -m "提交说明"
```
3. 查看文件状态：
```bash
git status
```


##  4. <a name='-1'></a>四、**关联远程仓库**

###  4.1. <a name='GitHub-1'></a>在 GitHub 上创建仓库

1. 点击 **New** 新建仓库
2. 输入仓库名称 → **Create repository**
   - 不建议勾选初始化选项（如 README）
3. 复制远程仓库的 URL（SSH 格式）

###  4.2. <a name='-1'></a>关联本地与远程仓库

```bash
git remote add origin git@github.com:用户名/仓库名.git
git remote -v   # 查看关联状态
```


##  5. <a name='-1'></a>五、上传项目到远程仓库

###  5.1. <a name='-1'></a>查看本地分支名

```bash
git branch
```

###  5.2. <a name='-1'></a>根据分支名推送

·如果分支名为 `main`：
```bash
git push -u origin main
```
·如果分支名为 `master`（或其他名称）：
```bash
git push -u origin master:main
```
·输入 SSH 密钥密码后推送成功
·刷新 GitHub 仓库页面确认文件已上传


##  6. <a name='-1'></a>六、**完成**

至此，已完成 Git 基本操作：从本地初始化到推送到远程仓库。