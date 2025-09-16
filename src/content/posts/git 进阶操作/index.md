---
title: git 进阶操作
published: 2025-06-10
description: "How to use git"
image: "./cover.jpeg"
tags: ["git",]
category: git
draft: false
---

> Cover image source: [Source](https://image.civitai.com/xG1nkqKTMzGDvpLrqFT7WA/208fc754-890d-4adb-9753-2c963332675d/width=2048/01651-1456859105-(colour_1.5),girl,_Blue,yellow,green,cyan,purple,red,pink,_best,8k,UHD,masterpiece,male%20focus,%201boy,gloves,%20ponytail,%20long%20hair,.jpeg)

# **目录**
<!-- vscode-markdown-toc -->
* 1. [**1、分支(branch)**](#1branch)
* 2. [**2、仓库（remote）**](#2remote)
* 3. [**3、冲突处理**](#3-1)
* 4. [**4、忽略文件**](#4-1)
* 5. [**5、撤销**](#5-1)
* 6. [**以下是我遇到过的问题和如何解决的**](#-1)
	* 6.1. [**-更新远程仓库内容到本地仓库**](#-)
	* 6.2. [**合并远程仓库分支**](#-1)


# **git进阶操作**

##  1. <a name='1branch'></a>**分支(branch)**

###  1.1 **查看分支：**

```
git branch #查看本地分支，带有\*的为当前分支
git branch -r #查看远程分支
git branch -a #查看所有分支
```

###  1.2 **新建分支：**


```
git branch <branch-name>
```

###  1.3 **切换分支：**

```
git checkout <bransh-name>
```

###  1.4 **新建并切换：**

```
git branch -b <branch-name>
```

###  1.5 **合并分支：**

```
git merge <branch-name> #合并指定分支到当前分支上
git merge --no-ff <branchname> #创建一个新的合并提交，并保留合并历史
git merge --abort #取消当前合并操作，恢复到合并之前的状态
```

###  1.6 **删除分支：**

```
git branch -d <branch-name> #删除一个已经合并的分支
git branch -D <bransh-name> #删除一个分支，无论是否合并
```
--------------------------------------------

##  2. <a name='2remote'></a>**仓库（remote）**
###  2.1 **初始化仓库：**

```
git init
```

###  2.2 **克隆仓库：**

```
git clone <repository-url>
```

###  2.3 **关联远程仓库：**

```
git remote add <renote-name> <repository-url>
```

###  2.4 **查看关联的远程仓库**

```
git remote -v
```

###  2.5 **推送到远程仓库：**

```
gut push #默认将当前分支推送到origin的main分支上
git push <remote> <branch-name>:<repository-branchname> #指定分支推送到指定远程仓库的指定分支
```

###  2.6 **从远程仓库拉取最新的更改合并到本地分支：**

```
git pull <remote-name> <branch-name>
```


##  3. <a name='3-1'></a>**冲突处理**

###  3.1 **检查冲突状态：**
  
```
git status
```
:::caution
**可能会显示：**
`You have unmerged paths
  	(use "git add <file>..." to mark resolution)
  	Unmerged paths:
  	both modified: file_name.txt
`
:::
###  3.2 **手动解决：**
打开冲突文件（例如： file_name.txt），会看到以下内容：

```
  <<<<<<< HEAD
  当前分支的更改
  =======
  另一个分支的更改
  >>>>>>> branch-name
```
编辑文件，删除冲突标记`（<<<<<<<、======= 和 >>>>>>>）`，并保留最终需要的内容。

###  3.3 **标记文件已经解决：**

```
  git add file_name.txt
```
###  3.4 **提交更改：**

```
  git commit -m "Resolved merge conflicts"
```


##  4. <a name='4-1'></a>**忽略文件**
:::tip
 **git中的三类文件**
- 被追踪的（tracked）:已经加入文档库
- 不被追踪的(untracked):没有加入文档库
- 忽略的(ignored):忽略那些不需要管理的文件夹或文件
:::

### **新建忽略文件**

- <font color=green>第一种办法：</font>直接项目根目录下直接右键新建.gitignore文件

- <font color=green>第二种办法：</font>项目根目录下操作：
  
```
touch .gitignore
```

##  5. <a name='5-1'></a>**撤销**

###  5.1 **撤销未暂存的修改（未使用git add ）**
如果文件已被修改但未添加到暂存区，可以使用以下命令撤销更改：
  
```
git checkout -- <文件路径>
```

- 撤销所有文件更改：
  
```
git checkout
```

###  5.2 **撤销已暂存但未提交的更改（已使用git add ）**
- <font color=green>[1]撤销暂存：</font>
  
```
git reset HEAD <fileName>
```

- <font color=green>[2]恢复文件：</font>
  
```
git checkout -- <fileName>
```

###  5.3 **已提交但需要回退到之前的状态**
**<font color=red>使用 `git reset --hard` 会丢失所有未保存的更改，请谨慎操作</font>**

 - <font color=#008000>[1]回退到上一个状态:</font>
  
```
git reset --hard HEAD^
```

 - <font color=#008000>[2]回退到指定状态：</font>

```
git log --<fileName> #查询提交历史并获取commit-id
git reset --hard <commit-id> #reset到指定commit
```
:::tip

**恢复丢失的更改（VSCode）**

打开项目文件 ->按 `Ctrl+Shift+P` ->输入`Local History` ->选择历史版本进行恢复。

:::


-------------------------------
##  6. <a name='-1'></a>**以下是我遇到过的问题**

###  6.1 <a name='-'></a>**更新远程仓库内容到本地仓库**
 - 1．获取远程仓库的最新信息：

```
git fetch –all
```
 - 2.合并远程更改到本地分支

```
git pull origin master
```
 - <font color=red>3.强制将本地代码重置为远程仓库的状态（慎选）</font>：

```
git reset --hard origin/master
```
 - 4.检查当前分支状态以确保同步成功：

```
git status
```


###  6.2 <a name='-1'></a>**合并远程仓库分支**
:::note
- **起因是之前推送的时候推送到了master分支但是git默认分支是main，我觉得观感不好。**
- **我的思路是**:新建一个本地仓库，将main远程分支的内容拉取过来，然后在本地将master分支合并到main分支上，在把合并好的新分支推送到远程main分支上就好啦。
:::


  
- 1.首先查看当前仓库分支

```
  git branch(本地仓库)
  git branch -a(远程仓库) 
```

- 2.创建一个新的本地仓库

```
  git checkout main 
```

- 3.拉取远程仓库的main分支内容

```
  git pull origin main 
```

- 4.合并仓库

```
  git merge master
```

  这个时候发现报错`fatal: refusing to merge unrelated histories`，原因是两个分支没有相同的历史记录。
  
  - 5.执行强制合并
  
```
git merge master --allow-unrelated-histories
```

  之后仍然报错`Auto-merging .gitignore`，发现原来是两个分支都存在的.gitignore文件有不同（这是因为之后有修改了一次.gitignore），这时候把.gitignore进行修改就可以解决冲突啦。之后只需要把.gitignore重新添加一遍。
- 6.重新添加.gitignore
  
```
git add .gitignore
git commit -m "合并master分支，解决.gitignore冲突"
```
 - 7.删除本地不需要的分支
  
```
git branch #查看本地分支
git branch -D master #删除master分支
```
- 8.将合并好的分支推送到远程main分支
  
```
git push origin main 
```
- 9.查看github看到main分支上内容正确（可以选择把不用的分支进行删除）
--------------------------------------------------------------------------