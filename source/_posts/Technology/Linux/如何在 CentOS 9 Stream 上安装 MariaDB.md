---
title: 如何在 CentOS 9 Stream 上安装 MariaDB
date: 2022-07-22 22:26:05
updated: 2022-07-23 02:12:05
description: 如何在 CentOS 9 Stream 上安装 MariaDB
keywords: Linux
cover: https://cdn.zhaifanhua.com/blog/img/202207230212968.png
top_img: https://cdn.zhaifanhua.com/blog/img/202207230212968.png
tags:
  - Linux
  - MariaDB
categories:
  - 服务器
---



# 如何在 CentOS 9 Stream 上安装 MariaDB



> MariaDB 是一个免费的开源数据库管理系统，可以替代 Oracle MySQL 服务器。MariaDB 是 MySQL 的开发，它专注于稳定性和性能，并免费提供给用户。

![Mariadb-CentOS](https://cdn.zhaifanhua.com/blog/img/202207230212965.jpg)

## 步骤

在 CentOS 9 Stream 上安装 MariaDB

### 步骤 1.

首先，让我们首先确保您的系统是最新的。

```
sudo dnf update
```

![image-20220722223142282](https://cdn.zhaifanhua.com/blog/img/202207230212967.png)

出现Complete即更新成功，为保证顺利运行，请重启系统。

```
reboot
```

### 步骤 2.

默认情况下，CentOS 9 Stream 基础存储库中提供了 MariaDB。现在我们运行以下命令将 MariaDB 安装到您的系统：

```
sudo dnf install mariadb-server
```

![image-20220722223057213](https://cdn.zhaifanhua.com/blog/img/202207230212968.png)

安装完成后检查版本：

```
mariadb --version
```

![image-20220722223446830](https://cdn.zhaifanhua.com/blog/img/202207230212969.png)

安装完成后，现在启用 MariaDB（系统启动时自动启动），启动 MariaDB，并使用以下命令验证状态：

```
sudo systemctl start mariadb
sudo systemctl enable mariadb
sudo systemctl status mariadb
```

![image-20220722223600841](https://cdn.zhaifanhua.com/blog/img/202207230212970.png)

### 步骤 3. 

安全 MariaDB 安装。

默认情况下，MariaDB 未加固。`mysql_secure_installation`您可以使用脚本保护 MariaDB 。您应该仔细阅读下面的每个步骤，这些步骤将设置 root 密码、删除匿名用户、禁止远程 root 登录、删除测试数据库和访问安全 MariaDB：

```
mysql_secure_installation
```

像这样配置它：

为了登录MariaDB以确保其安全，我们需要当前根用户的密码。如果您刚刚安装了MariaDB尚未设置根密码，您应该在此处按enter键。

> 我们刚安装，直接回车。

![image-20220722231526802](https://cdn.zhaifanhua.com/blog/img/202207230212971.png)

设置根密码或使用unix_socket套接字可以确保在没有适当授权的情况下登录到MariaDB root用户。

> 这里我为了方便设置了y，你可根据自身情况设置。

![image-20220722235655707](https://cdn.zhaifanhua.com/blog/img/202207230212972.png)

更改根密码，注意输入密码时不可见，须确认输入第二遍。

> 这里更改密码，设置y。之后输入密码。

![image-20220722232112955](https://cdn.zhaifanhua.com/blog/img/202207230212973.png)

默认情况下，MariaDB安装有匿名用户，允许任何人登录MariaDB而不必为其创建用户帐户他们这仅用于测试和安装再平稳一点。在移动到生产环境。

> 删除匿名用户，这里我删除了，设置了y。

![image-20220722232340835](https://cdn.zhaifanhua.com/blog/img/202207230212974.png)

通常，只允许root从“localhost”连接。这确保有人无法从网络中猜测根密码。

> 是否允许根用户远程登录，我需要远程登录，所以这了选择了n。

![image-20220722225846453](https://cdn.zhaifanhua.com/blog/img/202207230212975.png)

默认情况下，MariaDB附带了一个名为“test”的数据库，任何人都可以使用它通道这也仅用于测试，应删除在进入生产环境之前。

> 这里我不用测试，直接删除，选择了y。

![image-20220722235814179](https://cdn.zhaifanhua.com/blog/img/202207230212976.png)

重新加载特权表，将确保以上的所有更改将立即生效。

> 现在重新加载权限表，选择y。

![image-20220722230312015](https://cdn.zhaifanhua.com/blog/img/202207230212977.png)

到此，安装成功！可以直接用用户【root】和密码【你的密码】登录 MariaDB 了，请使用以下命令（请注意，它与登录 MariaDB 数据库的命令相同）：

```
mysql -u root -p
```

![image-20220722232556122](https://cdn.zhaifanhua.com/blog/img/202207230212978.png)

### 步骤 4.

用 root 账户启用到 MariaDB 数据库的远程连接：

```
## 授予权限
grant all on *.* to "root"@"%" identified by "你的root账户密码";

## 刷新权限
FLUSH PRIVILEGES;

## 查询
show grants for "root"@"%";
```

用 Navicat 测试一下

![image-20220723020856932](https://cdn.zhaifanhua.com/blog/img/202207230212979.png)

大功告成！
