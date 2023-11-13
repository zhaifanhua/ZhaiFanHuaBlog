---
title: GitLab的安装与部署
date: 2021-08-18 05:14:05
updated: 2022-01-05 10:15:05
description: GitLab的安装与部署
keywords: GitLab,安装与部署
cover: https://cdn.zhaifanhua.com/blog/img/202108172337072.png
top_img: https://cdn.zhaifanhua.com/blog/img/202108172337072.png
tags:
  - GitLab
categories:
  - 教程
---



## 1.GitLab介绍

#### 1.1.GitLab：

> 是一个基于Git实现的在线代码仓库托管软件，你可以用gitlab自己搭建一个类似于Github一样的系统，一般用于在企业、学校等内部网络搭建git私服。

- 功能：Gitlab 是一个提供代码托管、提交审核和问题跟踪的代码管理平台。对于软件工程质量管理非常重要。
- 版本：GitLab 分为社区版（CE） 和企业版（EE）。
- 配置：建议CPU2核，内存2G以上。

#### 1.2.Gitlab的服务构成：

- Nginx：静态web服务器。
- gitlab-shell：用于处理Git命令和修改authorized keys列表。（Ruby）
- gitlab-workhorse: 轻量级的反向代理服务器。（go，GitLab Workhorse是一个敏捷的反向代理。它会处理一些大的HTTP请求，比如文件上传、文件下载、Git push/pull和Git包下载。其它请求会反向代理到GitLab Rails应用，即反向代理给后端的unicorn。)
- logrotate：日志文件管理工具。
- postgresql：数据库。
- redis：缓存数据库。
- sidekiq：用于在后台执行队列任务（异步执行）。（Ruby）
- unicorn：An HTTP server for Rack applications，GitLab Rails应用是托管在这个服务器上面的。（Ruby Web Server,主要使用Ruby编写）

## 2.GitLab安装

#### 2.1.源码安装

##### 2.1.1.安装并配置必要的依赖

在 CentOS 7（和 RedHat/Oracle/Scientific Linux 7）上，下面的命令也会在系统防火墙中打开 HTTP、HTTPS 和 SSH 访问。这是一个可选步骤，如果您打算仅从本地网络访问 GitLab，则可以跳过它。

```
sudo yum install -y curl policycoreutils-python openssh-server perl
# 如果未启用，则启用OpenSSH服务器守护程序：sudo systemctl status sshd
sudo systemctl enable sshd
sudo systemctl start sshd

# 检查是否需要打开防火墙：sudo systemctl status firewalld
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo systemctl reload firewalld
```

接下来，安装 Postfix 以发送通知电子邮件。如果您想使用其他解决方案发送电子邮件，请跳过此步骤并在安装 GitLab 后[配置外部 SMTP 服务器](https://docs.gitlab.com/omnibus/settings/smtp.html)。

```
sudo yum install postfix
sudo systemctl enable postfix
sudo systemctl start postfix
```

在 Postfix 安装过程中，可能会出现一个配置屏幕。选择“Internet 站点”并按 Enter。

将您服务器的外部 DNS 用于“邮件名称”，然后按 Enter。如果出现其他屏幕，请继续按 Enter 接受默认值。

##### 2.1.2.添加GitLab包仓库并安装包

添加 GitLab 包存储库。

```
curl https://packages.gitlab.com/install/repositories/gitlab/gitlab-ee/script.rpm.sh | sudo bash
```

接下来，安装 GitLab 包。确保您已正确[设置 DNS](https://docs.gitlab.com/omnibus/settings/dns.html)，并更改`https://gitlab.example.com`为您要访问 GitLab 实例的 URL。安装将在该 URL 上自动配置和启动 GitLab。
对于`https://`URL，GitLab 将[使用 Let's Encrypt](https://docs.gitlab.com/omnibus/settings/ssl.html#lets-encrypthttpsletsencryptorg-integration)自动[请求证书](https://docs.gitlab.com/omnibus/settings/ssl.html#lets-encrypthttpsletsencryptorg-integration)，这需要入站 HTTP 访问和[有效的主机名](https://docs.gitlab.com/omnibus/settings/dns.html)。您也可以[使用自己的证书](https://docs.gitlab.com/omnibus/settings/nginx.html#manually-configuring-https)或仅使用`http://`（不带`s`）。
如果您想为初始管理员用户 ( `root`)指定自定义密码，请查看[文档](https://docs.gitlab.com/omnibus/installation/index.html#set-up-the-initial-password)。如果未指定密码，将自动生成随机密码。

```
sudo EXTERNAL_URL="https://gitlab.example.com" yum install -y gitlab-ee
```

##### 2.1.3.浏览到主机名并登录

除非您在安装过程中提供了自定义密码，否则将随机生成一个密码并在`/etc/gitlab/initial_root_password`. 使用此密码和用户名`root`登录。有关[安装和配置的详细说明，](https://docs.gitlab.com/omnibus/README.html#installation-and-configuration-using-omnibus-package)请参阅我们的[文档](https://docs.gitlab.com/omnibus/README.html#installation-and-configuration-using-omnibus-package)。

完成安装后，请考虑[建议的后续步骤](https://docs.gitlab.com/ee/install/next_steps.html)，包括身份验证选项和注册限制。

#### 2.2.包安装

##### 2.2.1.安装并配置必要的依赖（同2.1.源码安装的2.1.1.安装并配置必要的依赖）

##### 2.2.2.查看系统信息，下载对应的GitLab安装包

```
uname -a
```

![image-20210817223621085](https://cdn.zhaifanhua.com/blog/img/202108172236480.png)

可以看到我这里是 Linux VM-0-14-centos 3.10.0-1160.31.1.el7.x86_64 #1 SMP Thu Jun 10 13:32:12 UTC 2021 x86_64 x86_64 x86_64 GNU/Linux，那么就需要下载el7.x86_64.rpm的社区安装包。这里我下载了截至2021/8/17最新的安装包（gitlab-ce-13.12.10-ce.0.el7.x86_64.rpm）。

下载包（这里推荐先本地下载，再上传服务器，下载地址：[下载](https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-13.12.10-ce.0.el7.x86_64.rpm/download.rpm)）：

```
wget --content-disposition https://packages.gitlab.com/gitlab/gitlab-ce/packages/el/7/gitlab-ce-13.12.10-ce.0.el7.x86_64.rpm/download.rpm
```

这里我是本地下载，再上传服务器的，上传的目录为（/home/lib）

![image-20210817231129912](https://cdn.zhaifanhua.com/blog/img/202108172311381.png)

##### 2.2.3.安装GitLab 软件包

```
rpm -Uvh /home/libgitlab-ce-13.12.10-ce.0.el7.x86_64.rpm
```

##### 2.2.4.配置域名： 

```
vim /etc/gitlab/gitlab.rb
```

![image-20210817233745549](https://cdn.zhaifanhua.com/blog/img/202108172337072.png)

如上图，我的域名为 https://git.zhaifanhua.com 

#### 配置生效

```
# 使配置生效
sudo gitlab-ctl reconfigure
# 重新启动服务
sudo gitlab-ctl restart
```

## 3.外部Nginx反向代理Gitlab内部Nginx

由于Gitlab在安装的时候内部集成了Nginx，一般情况下，采用外部Nginx反向代理的思路来统一管理。

### 编辑gitlab配置文件

- 1、让gitlab的内置nginx监听7000端口

```
  vim /etc/gitlab/gitlab.rb
  nginx['listen_port'] = 7000
```

- 2、 设置gitlab的访问路径（是通过外部nginx反向代理访问的）

```
  external_url 'http://域名/gitlab'
```

- 3、 让配置生效

```
  gitlab-ctl reconfigure
  gitlab-ctl restart
```

### 配置外部nginx

- 1、编辑nginx配置文件

```
vim /etc/nginx/nginx.conf
```

```
http {
    ...
   server {
        listen 80;
        server_name 192.168.3.101;
        charset utf-8;
        add_header Strict-Transport-Security "max-age=63072000" always;
        #-------------代码------------------
        location /gitlab {
            # 设置最大允许上传单个的文件大小
            client_max_body_size 1024m;
            proxy_redirect off;
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            # 反向代理到 gitlab 内置的 nginx
            proxy_pass  http://127.0.0.1:7000;
            index index.html index.htm;
        }
    }
    ...
}
```

- 2、重启nginx

```
  # 检测配置是否正确
  nginx -t
    
  # 重启nginx
  nginx -s reload  # or service nginx restart
```

## 4.GitLab管理常用命令

```bash
# 禁止 Gitlab 开机自启动
systemctl disable gitlab-runsvdir.service
# 启用 Gitlab 开机自启动
systemctl enable gitlab-runsvdir.service

gitlab-ctl start    # 启动所有 gitlab 组件；
gitlab-ctl stop        # 停止所有 gitlab 组件；
gitlab-ctl restart        # 重启所有 gitlab 组件；
gitlab-ctl status        # 查看服务状态；
vim /etc/gitlab/gitlab.rb        # 修改gitlab配置文件；
gitlab-ctl reconfigure        # 重新编译gitlab的配置；
gitlab-rake gitlab:check SANITIZE=true --trace    # 检查gitlab；
gitlab-ctl tail        # 查看日志；
gitlab-ctl tail nginx/gitlab_access.log
```
