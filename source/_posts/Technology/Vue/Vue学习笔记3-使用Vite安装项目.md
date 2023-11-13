---
title: Vue学习笔记3-使用Vite安装项目
date: 2022-06-12 20:49:15
updated: 2022-06-12 20:52:58
description:  Vite是一个 web 开发构建工具，由于其原生 ES 模块导入方式，可以实现闪电般的冷服务器启动。
keywords: Vue，前端,学习
cover: https://cdn.zhaifanhua.com/blog/img/202206120301401.png
top_img: https://cdn.zhaifanhua.com/blog/img/202206120301401.png
tags:
  - Vue
categories:
  - Vue
---



# Vue学习笔记3-使用Vite安装项目

## 一、Vite

[Vite](https://cn.vitejs.dev/) 是一个 web 开发构建工具，由于其原生 ES 模块导入方式，可以实现闪电般的冷服务器启动。

通过在终端中运行以下命令，可以使用 Vite 快速构建 Vue 项目。

使用 npm：

```bash
# npm 6.x
$ npm init vite@latest <project-name> --template vue

# npm 7+，需要加上额外的双短横线
$ npm init vite@latest <project-name> -- --template vue

$ cd <project-name>
$ npm install
$ npm run dev
```

或者 yarn：

```bash
$ yarn create vite <project-name> --template vue
$ cd <project-name>
$ yarn
$ yarn dev
```

或者 pnpm:

```bash
$ pnpm create vite <project-name> -- --template vue
$ cd <project-name>
$ pnpm install
$ pnpm dev
```

## 二、安装

> 前提，先安装nodejs,安装完成后就自动安装了npm，若运行时出现 `【不是内部或外部命令，也不是可运行的程序或批处理文】`的报错，原因是windows执行策略不允许不安全的脚本运行,可以用以下方式解决。
>

脚本运行权限政策有以下几种：

> Restricted      ——默认的设置， 不允许任何脚本运行
> AllSigned       ——只能运行经过数字证书签名的脚本
> RemoteSigned    ——运行本地的脚本不需要数字签名，但是运行从网络上下载的script就必须要有数字签名
> Unrestricted    ——允许所有的脚本运行

在开始菜单里找到Windows PowerShell，并以管理员身份运行

![image-20220612020737325](https://cdn.zhaifanhua.com/blog/img/202206120301396.png)

我们可以通过以下命令查看我们计算机的所有策略：

```
Get-ExecutionPolicy -List
```

![image-20220612022322629](https://cdn.zhaifanhua.com/blog/img/202206120301398.png)

可以发现本地和用户都是默认策略，此策略不允许任何脚本运行。我们可以通过以下命令查看我们计算机的用户策略：

```
Get-ExecutionPolicy
```

![image-20220612022645061](https://cdn.zhaifanhua.com/blog/img/202206120301399.png)

所以我们需要允许本地和用户运行签名脚本，用以下命令：

```
Set-ExecutionPolicy RemoteSigned CurrentUser
Set-ExecutionPolicy RemoteSigned LocalMachine
Get-ExecutionPolicy -List
```

![image-20220612022915812](https://cdn.zhaifanhua.com/blog/img/202206120301400.png)

这时，表明策略更改成功，就可以运行脚本了。

### 1.npm方式

#### 1.1 切换镜像

当下，如果没有网速低，那又何谈幸福！使用以下命令，让下载速度快起来。

```
npm config set registry https://registry.npmmirror.com
```

> 不建议使用cnpm，因为安装依赖或使用别的项目可能会出现概率性报错。

#### 1.2 安装项目

初始化

```
npm init vite@latest demo-npm -- --template vue
```

进入目录

```
cd .\demo-npm\
```

安装

```
npm install
```

![image-20220612023636302](https://cdn.zhaifanhua.com/blog/img/202206120301401.png)

运行

```
npm run dev
```

出现以下提示，表示运行成功

```
  vite v2.9.12 dev server running at:

  > Local: http://localhost:3000/
  > Network: use `--host` to expose

  ready in 226ms.
```

在浏览器打开

![image-20220612010505096](https://cdn.zhaifanhua.com/blog/img/202206120301402.png)

项目树结构

```
Demo
└─ demo-npm
  ├─ node_modules             --项目依赖文件夹
  │  └─ .bin
  │  └─ vue
  │  └─ ……
  ├─ public                   --静态资源文件夹（如图标和不支持 JavaScript 情况时的页面等）
  │  └─ favicon.ico
  ├─ src                      --源代码文件夹
  │  ├─ App.vue                  --项目根组件文件（所有页面都是在该组件下进行切换的）
  │  ├─ assets                   --资源文件夹（存放 css，img等资源）
  │  │  └─ logo.png
  │  ├─ components               --公共组件文件夹
  │  │  └─ HelloWorld.vue
  │  └─ main.js                  --项目入口文件（作用是初始化 vue 实例，并引入所需要的插件）
  ├─ index.html               --入口页面文件
  ├─ package-lock.json        --依赖关系树文件
  ├─ package.json             --项目依赖配置文件（比如 vuex，element-UI）
  ├─ vite.config.js           --Vite配置文件
  └─ README.md                --项目介绍文件
```

### 2.yarn方式

>Yarn是facebook发布的一款取代npm的包管理工具。
>
>yarn的特点：
>速度超快。
>Yarn 缓存了每个下载过的包，所以再次使用时无需重复下载。 同时利用并行下载以最大化资源利用率，因此安装速度更快。
>超级安全。
>在执行代码之前，Yarn 会通过算法校验每个安装包的完整性。
>超级可靠。
>使用详细、简洁的锁文件格式和明确的安装算法，Yarn 能够保证在不同系统上无差异的工作。

#### 2.1 安装yarn

安装yan，用以下命令：

```
 npm install yarn --location=global
```

查看版本：

```
yarn --version
```

![image-20220612024630371](https://cdn.zhaifanhua.com/blog/img/202206120301403.png)

#### 2.2 安装项目

初始化

```
yarn create vite demo-yarn --template vue
```

进入目录

```
cd .\demo-yarn\
```

安装

```
yarn
```

运行

```
yarn dev
```

在浏览器打开

![image-20220612010505096](https://cdn.zhaifanhua.com/blog/img/202206120301402.png)

## 源码

[查看源码](https://github.com/zhaifanhua/Learning-Vue/tree/main/3.Install%20with%20Vite)
