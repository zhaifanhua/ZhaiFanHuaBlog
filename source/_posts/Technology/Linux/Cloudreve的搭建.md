---
title: Cloudreve的搭建
date: 2021-08-25 14:14:05
updated: 2021-08-25 15:15:05
description: Cloudreve的搭建
keywords: Cloudreve
cover: https://cdn.zhaifanhua.com/blog/img/202108181552967.png
top_img: https://cdn.zhaifanhua.com/blog/img/202108181552967.png
tags:
  - 网盘
categories:
  - 教程
---



# 1.获取 Cloudreve

你可以在 [GitHub Release](https://github.com/cloudreve/Cloudreve/releases) 页面获取已经构建打包完成的主程序。其中每个版本都提供了常见系统架构下可用的主程序，命名规则为`cloudreve_版本号_操作系统_CPU架构.tar.gz` 。比如，普通64位Linux系统上部署3.0.0版本，则应该下载`cloudreve_3.0.0_linux_amd64.tar.gz`。

如果你想体验最新的功能特性，可以在 [GitHub Actions](https://github.com/cloudreve/Cloudreve/actions) 中下载每次 commit 后构建的开发版。注意，开发版并不稳定，无法用于生产用途，且不保证完全可用。获取 Cloudreve

你可以在 [GitHub Release](https://github.com/cloudreve/Cloudreve/releases) 页面获取已经构建打包完成的主程序。其中每个版本都提供了常见系统架构下可用的主程序，命名规则为`cloudreve_版本号_操作系统_CPU架构.tar.gz` 。比如，普通64位Linux系统上部署3.0.0版本，则应该下载`cloudreve_3.0.0_linux_amd64.tar.gz`。

如果你想体验最新的功能特性，可以在 [GitHub Actions](https://github.com/cloudreve/Cloudreve/actions) 中下载每次 commit 后构建的开发版。注意，开发版并不稳定，无法用于生产用途，且不保证完全可用。

# 2.启动 Cloudreve

Linux下，直接解压并执行主程序即可：

```
#解压获取到的主程序
tar -zxvf cloudreve_VERSION_OS_ARCH.tar.gz
# 赋予执行权限
chmod +x ./cloudreve
# 启动Cloudreve
./cloudreve
```

Cloudreve 在首次启动时，会创建初始管理员账号，请注意保管管理员密码，此密码只会在首次启动时出现。如果您忘记初始管理员密码，需要删除同级目录下的`cloudreve.db`，重新启动主程序以初始化新的管理员账户。

Cloudreve 默认会监听`5212`端口。你可以在浏览器中访问`http://服务器IP:5212`进入 Cloudreve。

以上步骤操作完后，最简单的部署就完成了。你可能需要一些更为具体的配置，才能让Cloudreve更好的工作，具体流程请参考下面的配置流程。

# 3.可选部署流程

## 3.1.反向代理

在自用或者小规模使用的场景下，你完全可以使用 Cloudreve 内置的 Web 服务器。但是如果你需要使用HTTPS，亦或是需要与服务器上其他 Web 服务共存时，你可能需要使用主流 Web 服务器反向代理 Cloudreve ，以获得更丰富的扩展功能。

你需要在Web服务器中新建一个虚拟主机，完成所需的各项配置（如启用HTTPS），然后在网站配置文件中加入反代规则：

NGINX，在网站的`server`字段中加入：

```
location / {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_redirect off;
    proxy_pass http://127.0.0.1:5212;

    # 如果您要使用本地存储策略，请将下一行注释符删除，并更改大小为理论最大文件尺寸
    # client_max_body_size 20000m;
}
```

## 3.2.进程守护

### 3.2.1.Systemd

```
# 编辑配置文件
vim /usr/lib/systemd/system/cloudreve.service
```

将下文 `PATH_TO_CLOUDREVE` 更换为程序所在目录：

```
[Unit]
Description=Cloudreve
Documentation=https://docs.cloudreve.org
After=network.target
After=mysqld.service
Wants=network.target

[Service]
WorkingDirectory=/PATH_TO_CLOUDREVE
ExecStart=/PATH_TO_CLOUDREVE/cloudreve
Restart=on-abnormal
RestartSec=5s
KillMode=mixed

StandardOutput=null
StandardError=syslog

[Install]
WantedBy=multi-user.target
```

### 3.2.2.更新配置和启动服务

```
# 更新配置
systemctl daemon-reload
# 启动服务
systemctl start cloudreve
# 设置开机启动
systemctl enable cloudreve
```

### 3.2.3.启动报错情况处理

![image-20220215104701510](https://cdn.zhaifanhua.com/blog/img/202202151121393.png)

原因：是selinux中间移动了位置，导致selinux的安全上下文没切换，报权限不足。

解决办法：关掉selinux。

```
getenforce
```

如果为disabled 就是已经关闭，如果enforce 就是强制的模式。

（方法1）不重启的头闭，重启电脑后失效。

```
setenforce　0
```

（方法2）重启电脑的关闭，重启电脑，永久生效。

```
vi /etc/selinux/config
# 把SELINUX=enforce改成disabled就可以了
```

# 4.管理命令：

```
# 启动服务
systemctl start cloudreve
# 停止服务
systemctl stop cloudreve
# 重启服务
systemctl restart cloudreve
# 查看状态
systemctl status cloudreve
```

![image-20210818155215456](https://cdn.zhaifanhua.com/blog/img/202108181552967.png)
