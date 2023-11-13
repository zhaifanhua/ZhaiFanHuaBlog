---
title: CentOS上部署自己的DotNet项目
date: 2021-09-30 19:59:05
updated: 2022-07-23 02:50:05
description: CentOS上部署自己的DotNet项目
keywords: CentOS,DotNet
cover: https://cdn.zhaifanhua.com/blog/img/202207230253805.png
top_img: https://cdn.zhaifanhua.com/blog/img/202207230253805.png
tags:
  - DotNet
  - CentOS
  - 服务器
categories:
  - 教程
---





# CentOS上部署自己的DotNet项目

## 1.查看环境

> 这里我的服务器系统是Linux【CentOS Stream 9】，具体版本对应的安装包请【[查看官网](https://docs.microsoft.com/zh-cn/dotnet/core/install)】,暂以CentOS Stream 9为例。

通过以下命令查看系统版本

```
uname -a 
```

![image-20220723025233825](https://cdn.zhaifanhua.com/blog/img/202207230253803.png)

## 2.安装 DotNet

.NET 包含在 CentOS Stream 9 的 AppStream 存储库中。 但是，.NET Core 3.1 和 .NET 5 已从 CentOS Stream 9 中删除，因此应使用 .NET 6。 有关详细信息，请参阅博客文章[在 CentOS Stream 9 中将 .NET 与 OpenSSL 结合使用 | Omair Majid](https://omairmajid.com/posts/2021-08-25-using-.net-in-centos-stream-9/)。

### 2.1.安装 SDK

.NET SDK 使你可以通过 .NET 开发应用。 如果安装 .NET SDK，则无需安装相应的运行时。 若要安装 .NET SDK，请运行以下命令：

```bash
sudo dnf install dotnet-sdk-6.0
```

### 2.2.安装运行时

通过 ASP.NET Core 运行时，可以运行使用 .NET 开发且未提供运行时的应用。 以下命令将安装 ASP.NET Core 运行时，这是与 .NET 最兼容的运行时。 在终端中，运行以下命令：

```bash
sudo dnf install aspnetcore-runtime-6.0
```

作为 ASP.NET Core 运行时的一种替代方法，你可以安装不包含 ASP.NET Core 支持的 .NET 运行时：将上一命令中的 `aspnetcore-runtime-6.0` 替换为 `dotnet-runtime-6.0`：

```bash
sudo dnf install dotnet-runtime-6.0
```

## 3.检查 SDK 版本

可使用终端查看当前安装的 .NET SDK 版本。 打开终端并运行以下命令。

```dotnetcli
dotnet --list-sdks
```

## 4.检查运行时版本

可使用以下命令查看当前安装的 .NET 运行时版本。

```dotnetcli
dotnet --list-runtimes
```

## 5.挂载服务

> 假如你有一个 DotNet 项目叫做【ZhaiFanhuaBlog】，部署上传在服务器的【/home/web/zhaifanhua.com/demo/ZhaiFanhuaBlog/】路径下。
>
> 该项目可执行文件名称是【ZhaiFanhuaBlog.WebApi.dll】，你想用ip【127.0.0.1】，url【http://*:9708】来访问。

把 DotNet 也变成一个像mysql，nginx一样的后台服务，用systemctl来管理。可以这样配置：

```
# 编辑配置文件
vim /usr/lib/systemd/system/ZhaiFanhuaBlog.service
```

### 5.1.编辑输入：

```
[Unit]
Description=ZhaiFanhuaBlog

[Service]
WorkingDirectory=/home/web/zhaifanhua.com/demo/ZhaiFanhuaBlog/
ExecStart=dotnet /home/web/zhaifanhua.com/demo/ZhaiFanhuaBlog/ZhaiFanhuaBlog.WebApi.dll --urls="http://*:9708" --ip="127.0.0.1"
Restart=always
RestartSec=10
KillSignal=SIGINT
SyslogIdentifier=ZhaiFanhuaImage
User=root
Environment=ASPNETCORE_ENVIRONMENT=Production
Environment=DOTNET_PRINT_TELEMETRY_MESSAGE=false

[Install]
WantedBy=multi-user.target
```

### 5.2.更新配置

```
systemctl daemon-reload
```

### 5.3.进程守护

```
# 设置开机自启动
systemctl enable ZhaiFanhuaBlog
# 停止开机自启动
systemctl disable ZhaiFanhuaBlog
```

![image-20220723024239729](https://cdn.zhaifanhua.com/blog/img/202207230253804.png)

### 5.4.管理命令

```
# 启动服务
systemctl start ZhaiFanhuaBlog
# 停止服务
systemctl stop ZhaiFanhuaBlog
# 重新服务
systemctl restart ZhaiFanhuaBlog
# 查看状态
systemctl status ZhaiFanhuaBlog
```

 ![image-20220723024148230](https://cdn.zhaifanhua.com/blog/img/202207230253805.png)

## 6.访问

### 6.1.直接用ip访问

![image-20220723024823675](https://cdn.zhaifanhua.com/blog/img/202207230253807.png)

### 6.2.用 Nginx 反代，用域名访问

![image-20220723025002294](https://cdn.zhaifanhua.com/blog/img/202207230253808.png)
