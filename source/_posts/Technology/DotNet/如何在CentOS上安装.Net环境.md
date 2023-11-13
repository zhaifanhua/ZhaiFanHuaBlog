---
title:  如何在CentOS上安装.Net环境
date: 2023-03-06 22:47:48
updated: 2023-03-06 22:47:48
description: 如何在CentOS上安装.Net环境
keywords: CentOS,DotNet
cover: https://cdn.zhaifanhua.com/blog/img/202303062249474.png
top_img: https://cdn.zhaifanhua.com/blog/img/202303062249474.png
tags:
  - CentOS
  - CSharp
  - DotNet
categories:
  - 教程
---



# 如何在CentOS上安装.Net环境

## 查看旧版本

使用命令查看安装了哪些版本

```shell
dotnet --list-sdks
dotnet --list-runtimes
```

![image-20230302103308955](https://cdn.zhaifanhua.com/blog/img/202303062249472.png)

## 安装

安装 .NET 之前，请运行以下命令，将 Microsoft 包签名密钥添加到受信任密钥列表，并添加 Microsoft 包存储库。 打开终端并运行以下命令：

```shell
sudo rpm -Uvh https://packages.microsoft.com/config/centos/7/packages-microsoft-prod.rpm
```

![image-20230302103621322](https://cdn.zhaifanhua.com/blog/img/202303062249473.png)

### 安装 SDK

.NET SDK 使你可以通过 .NET 开发应用。 如果安装 .NET SDK，则无需安装相应的运行时。 若要安装 .NET SDK，请运行以下命令：

```bash
sudo yum install dotnet-sdk-7.0
```

![image-20230302110929965](https://cdn.zhaifanhua.com/blog/img/202303062249474.png)

### 安装运行时

通过 ASP.NET Core 运行时，可以运行使用 .NET 开发且未提供运行时的应用。 以下命令将安装 ASP.NET Core 运行时，这是与 .NET 最兼容的运行时。 在终端中，运行以下命令：

```bash
sudo yum install aspnetcore-runtime-7.0
```

![image-20230302110336938](https://cdn.zhaifanhua.com/blog/img/202303062249475.png)

![image-20230302110353858](https://cdn.zhaifanhua.com/blog/img/202303062249476.png)

## 验证版本

用以下命令查看版本

```
dotnet --info
```

![image-20230302111055266](https://cdn.zhaifanhua.com/blog/img/202303062249477.png)

或用以下命令

```
dotnet --list-sdks
dotnet --list-runtimes
```

![image-20230302111205823](https://cdn.zhaifanhua.com/blog/img/202303062249478.png)

安装完成。