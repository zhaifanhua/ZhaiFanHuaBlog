---
title: 用ZFile搭建公共在线网盘
date: 2021-05-11 18:14:05
updated: 2021-05-12 00:15:05
description: 用zfile搭建公共在线网盘
keywords: zfile,搭建,公共,在线网盘
cover: https://cdn.zhaifanhua.com/blog/img/20210612042510.png
top_img: https://cdn.zhaifanhua.com/blog/img/20210612042510.png
tags:
  - 网盘
categories:
  - 教程
---



注：本文只用于个人使用教程，基本转载原作者文档配图以便后续更换服务器时配置，原文档地址: http://docs.zhaojun.im/zfile 。

## 环境配置

### 安装依赖

> 下载 java 环境。

```bash
# CentOS系统
yum install -y java-1.8.0-openjdk unzip
```

![image-20210612042503354](https://cdn.zhaifanhua.com/blog/img/20210612042510.png)

### 下载项目

> 这里我将文件下载到了 /home/www 下，并在该文件夹下创建了 pan 这个文件夹，并为此文件夹赋予操作权限

```bash
cd ~
wget -P /home/www https://c.jun6.net/ZFILE/zfile-release.war
cd /home/www
mkdir pan && unzip zfile-release.war -d pan && rm -rf zfile-release.war
chmod +x /home/www/pan/bin/*.sh
```

![image-20210612042621776](https://cdn.zhaifanhua.com/blog/img/20210612042624.png)

……

![image-20210612042706352](https://cdn.zhaifanhua.com/blog/img/20210612042708.png)

### 常用命令

```bash
/home/www/pan/bin/start.sh       # 启动项目
/home/www/pan/bin/stop.sh        # 停止项目
/home/www/pan/bin/restart.sh     # 重启项目
```

### 更改端口

> 由于 zfile 的默认端口是8080端口，这里我的这个端口已占用，所以需要改一下。如果你的站点少或该端口没有被占用，可以忽略这一步，不用更改。

1、用 xftp 打开 /home/www/pan/WEB-INF/classes 文件夹。

![image-20210612043404047](https://cdn.zhaifanhua.com/blog/img/20210612043406.png)

2、用任何编辑器打开 application.yml 文件，找到 server ，更改为你需要的端口。

> 为什么呢？因为端口范围一般用到的是1到65535，其中0不使用。端口号可分为3大类：
>
> 1、公认端口（Well Known Ports）：从0到1023，它们紧密绑定（binding）于一些服务。通常这些端口的通讯明确表明了某种服务的协议。例如：80端口实际上总是HTTP通讯。
>
> 2、注册端口（Registered Ports）：从1024到49151。它们松散地绑定于一些服务。也就是说有许多服务绑定于这些端口，这些端口同样用于许多其它目的。例如：许多系统处理动态端口从1024左右开始。
>
> 3、动态和/或私有端口（Dynamic and/or Private Ports）：从49152到65535。理论上，不应为服务分配这些端口。实际上，机器通常从1024起分配动态端口。但也有例外：SUN的RPC端口从32768开始。

![image-20210612044332360](https://cdn.zhaifanhua.com/blog/img/20210612044334.png)

为安全起见，这里我用 55555 。

![image-20210612053637243](https://cdn.zhaifanhua.com/blog/img/20210612065127.png)

### 修改 nginx.conf 文件

由于我用的域名是 https 访问，所以需要代理到 55555 端口，如果不用域名，这一步省略。如我的配置如下：

```
#-------------摘繁华公共网盘------------------
server {
    listen					443 ssl;
    server_name				pan.zhaifanhua.com;
    ssl_certificate			/home/ssl/zhaifanhua.com/full_chain.pem;
    ssl_certificate_key		/home/ssl/zhaifanhua.com/private.key;
    add_header Strict-Transport-Security "max-age=63072000" always;
    ssl_stapling            on;
    ssl_stapling_verify     on;
    ssl_session_cache		shared:SSL:1m;
    ssl_session_timeout		5m;
    ssl_protocols			TLSv1 TLSv1.1 TLSv1.2;
    ssl_ciphers				ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES256-GCM-SHA384:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-CHACHA20-POLY1305:ECDHE-RSA-CHACHA20-POLY1305:DHE-RSA-AES128-GCM-SHA256:DHE-RSA-AES256-GCM-SHA384;

    location / {
    proxy_pass          http://127.0.0.1:55555;
    }
}
```

修改完重启 nginx 即可。

```
systemctl restart nginx
```

### 配置完成

这时我们直接访问域名，即可进入安装页面。

![image-20210612055124775](https://cdn.zhaifanhua.com/blog/img/20210612055127.png)

安装完成配置好驱动器就可以访问了。

![image-20210612062125708](https://cdn.zhaifanhua.com/blog/img/20210612062128.png)



## 前后端分离部署

下载 `https://github.com/zhaojun1998/zfile/tree/master/src/main/resources/static` 路径下的所有文件, 或在程序运行后的相对路径：`WEB-INF/classes/static`.

修改 `zfile.config.json` 内文件指向后端地址, 然后放到静态资源服务器, 或对象存储即可:

```json
{
  "baseUrl": "http://xx.xxx.cn:8080"
}
```

![image-20210612062432546](https://cdn.zhaifanhua.com/blog/img/20210612062434.png)

