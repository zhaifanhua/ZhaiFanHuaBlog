---
title: 本地git仓库提交代码到远程git仓库
date: 2022-04-25 11:14:05
updated: 2022-04-25 11:15:05
description: 本地git仓库提交代码到远程git仓库
keywords: git
cover: https://cdn.zhaifanhua.com/blog/img/202204251057878.png
top_img: https://cdn.zhaifanhua.com/blog/img/202204251057878.png
tags:
  - git
categories:
  - 教程
---

 



# 本地git仓库提交代码到远程git仓库

在实际的开发中，有可能遇到需要将A仓库的某个branch的代码独立成为一个新的仓库B。我们需要的不仅仅是这个branch的最新提交的代码，而是需要它的整个提交历史。这种需求对于git来说非常简单，主要依靠`git remote`命令来实现。

## 1.初始化代码仓库提交到远程git仓库

```
git init
git add .
git commit -m "first commit"
git remote add origin git@server_ip:/path/repo.git
git push -u origin master
```

## 2.当前代码提交到另一个远程git仓库

假如仓库repo_a当前位于branch_a，要求将branch_a的整个数据（包括提交历史）全部提取出来，并建立一个新的仓库repo_b。这里假设仓库repo_b已经被建立。`git remote add`基本语法如下。name和url是必须的。

```
git remote add [-t <branch>] [-m <master>] [-f] [--[no-]tags] [--mirror=<fetch|push>] <name> <url>
```

1、 将仓库repo_b的URL添加到工作仓库的remote。`origin_repo_b`:自己起的名字，只要不与现有的remote名重复即可，`git@server_ip:/path/repo_b.git`:repo_b的远程路径。

```ruby
git remote add origin_repo_b git@server_ip:/path/repo_b.git
```

2、将代码推送到远程repo_b。`origin_repo_b`:远程仓库repo_b的名字，`branch_a`:仓库repo_a的的branch_a分支。

```perl
git push origin_repo_b branch_a
```

3、克隆仓库repo_b，检查是否push成功。

```ruby
git clone git@server_ip:/path/repo_b.git
```

## 3.一份相同的代码提交到多个不同的git托管服务器（多个git仓库）

方法非常相似，多使用到了命令`git remote set-url --add [--push] <name> <newurl>`，假设在远程的git服务器上又新增了一个仓库repo_c，现在要求repo_b、repo_c提交的代码必须一致。
1、将远程repo_c配置到当前的工作的本地git仓库中

```ruby
git remote set-url --add origin_repo_b git@192.168.1.101:~/project/repo_c.git
```

这句话的意思是，将远程仓库git@192.168.1.101:~/project/repo_c.git也加入到origin_repo_b这个名字下面管理

2、将代码同时提交到远程仓库repo_b和远程仓库repo_c

```perl
git push origin_repo_b branch_a
```

![Snipaste_2021-12-03_23-12-09](https://cdn.zhaifanhua.com/blog/img/202204251057878.png)