---
title:   用acme.sh自动部署域名证书
date: 2022-11-30 00:54:08
updated: 2022-11-30 00:59:24
description:  用acme.sh自动部署域名证书
keywords: acme.sh,域名证书
cover: https://cdn.zhaifanhua.com/blog/img/202211300049395.png
top_img: https://cdn.zhaifanhua.com/blog/img/202211300049395.png
tags:
  - Nginx
  - 域名
categories:
  - 教程
---





# 用acme.sh自动部署域名证书

## 安装ACME

目前使用量最大的免费`SSL`证书就是`Let’s Encrypt`，自`2018-03`开始，`Let’s Encrypt`官方发布上线了免费的`SSL`泛域名证书，目前通过`DNS`方式获取比较快，国内可以通过鹅云的`DNSPod`域名`API`或者猫云域名`API`自动签发`Let’s Encrypt`泛域名证书。因为鹅云使用的就是`DNSPod`域名，并且鹅云和[DNSPod](https://link.juejin.cn?target=https%3A%2F%2Fwww.dnspod.cn)的账号是打通的，可以使用`wx`直接扫码登录。下文需要对鹅云和`DNSPod`进行操作，为了简化证书申请过程，需要提前安装[acme.sh](https://link.juejin.cn?target=https%3A%2F%2Fgithub.com%2Facmesh-official%2Facme.sh)。`acme.sh`实现了`acme`协议，可以从`Let’s Encrypt`生成免费的证书，自动创建`cron`任务, 每天零点自动检测所有的证书，如果发现证书快过期了，需要更新，则`acme.sh`会自动更新证书，安装过程不会污染已有的系统任何功能和文件，所有的修改都限制在安装目录中。

先进行依赖下载和更新。如果服务器是`CentOS`系统，使用下面的命令：

```shell
yum update && yum install curl -y && yum install cron -y && yum install socat -y
```

如果服务器是`Debian/Ubuntu`系统，则使用下面的命令：

```shell
apt-get update && apt-get install curl -y && apt-get install cron -y && apt-get install socat -y
```

## 1. 安装 **acme.sh**

安装很简单, 一个命令:

```shell
curl https://get.acme.sh | sh -s email=yourEmail
```

配置自动解析

以 dnspod 为例, 你需要先登录到 dnspod 账号, 生成你的 api id 和 api key, 都是免费的。

![image-20221128121438606](https://cdn.zhaifanhua.com/blog/img/202211300049391.png)然后配置:

```shell
export DP_Id="apiid"
export DP_Key="apikey"
```

## 2. 生成证书

### 手动 dns 方式, 手动在域名上添加一条 txt 解析记录, 验证域名所有权。

这种方式的好处是, 你不需要任何服务器, 不需要任何公网 ip, 只需要 dns 的解析记录即可完成验证. 坏处是，如果不同时配置 Automatic DNS API，使用这种方式 acme.sh 将无法自动更新证书，每次都需要手动再次重新解析验证域名所有权。

```shell
~/.acme.sh/acme.sh --issue --dns -d zhaifanhua.com -d *.zhaifanhua.com --yes-I-know-dns-manual-mode-enough-go-ahead-please
```

然后, **acme.sh** 会生成相应的解析记录显示出来, 你只需要在你的域名管理面板中添加这条 txt 记录即可.

![image-20221128120542715](https://cdn.zhaifanhua.com/blog/img/202211300049392.png)

验证解析生效：

![image-20221128150010468](https://cdn.zhaifanhua.com/blog/img/202211300049393.png)

```shell
nslookup -q=txt _acme-challenge.zhaifanhua.com
```

![image-20221128150252182](https://cdn.zhaifanhua.com/blog/img/202211300049394.png)

等待解析完成之后, 重新生成证书:

```shell
~/.acme.sh/acme.sh --force --renew -d zhaifanhua.com -d *.zhaifanhua.com --yes-I-know-dns-manual-mode-enough-go-ahead-please
```

![image-20221128120812869](https://cdn.zhaifanhua.com/blog/img/202211300049395.png)

![image-20221128120833039](https://cdn.zhaifanhua.com/blog/img/202211300049396.png)

## 3. copy/安装 证书

前面证书生成以后, 接下来需要把证书 copy 到真正需要用它的地方：

![image-20221128124635187](https://cdn.zhaifanhua.com/blog/img/202211300049397.png)

注意, 默认生成的证书都放在安装目录下: `~/.acme.sh/`, 请不要直接使用此目录下的文件, 例如: 不要直接让 nginx/apache 的配置文件使用这下面的文件. 这里面的文件都是内部使用, 而且目录结构可能会变化。

正确的使用方法是使用 `--install-cert` 命令,并指定目标位置, 然后证书文件会被copy到相应的位置, 例如:

### Nginx:

执行命令，这里指定的所有参数都会被自动记录下来, 并在将来证书自动更新以后, 被再次自动调用。

配置 Nginx 文件：

![image-20221128130528181](https://cdn.zhaifanhua.com/blog/img/202211300049398.png)

这里我将其配置到了 /home/ssl 目录下，zhaifanhua.com_key.key 和 zhaifanhua.com_cert.pem 分别是私匙和证书名称，可自定义。

```
~/.acme.sh/acme.sh --install-cert -d zhaifanhua.com -d *.zhaifanhua.com --key-file /home/ssl/zhaifanhua.com_key.key --fullchain-file /home/ssl/zhaifanhua.com_fullchain.cer --reloadcmd "service nginx force-reload"
```

![image-20221128131040621](https://cdn.zhaifanhua.com/blog/img/202211300049399.png)

> (一个小提醒, 这里用的是 `service nginx force-reload`, 不是 `service nginx reload`, 据测试, `reload` 并不会重新加载证书, 所以用的 `force-reload`)
>
> Nginx 的配置 `ssl_certificate` 使用 `/etc/nginx/ssl/fullchain.cer` ，而非 `/etc/nginx/ssl/<domain>.cer` ，否则 [SSL Labs](https://www.ssllabs.com/ssltest/) 的测试会报 `Chain issues Incomplete` 错误。
>
> `--install-cert`命令可以携带很多参数, 来指定目标文件. 并且可以指定 reloadcmd, 当证书更新以后, reloadcmd会被自动调用,让服务器生效。
>
> 详细参数请参考: https://github.com/Neilpang/acme.sh#3-install-the-issued-cert-to-apachenginx-etc

显示 Reload success 表示成功！

![image-20221128131134054](https://cdn.zhaifanhua.com/blog/img/202211300049400.png)

## 4. 查看已安装证书信息

### 查看/删除证书

查看证书

```
~/.acme.sh/acme.sh --list
```

删除证书

```
~/.acme.sh/acme.sh remove <SAN_Domains>
```

查看信息

```shell
~/.acme.sh/acme.sh --info -d zhaifanhua.com -d *.zhaifanhua.com
```

![image-20221128134334244](https://cdn.zhaifanhua.com/blog/img/202211300049401.png)

## 5. 更新证书

目前证书在 60 天以后会自动更新, 你无需任何操作. 今后有可能会缩短这个时间, 不过都是自动的, 你不用关心.

请确保 cronjob 正确安装, 看起来是类似这样的:

```
crontab  -l

*/5 * * * * flock -xn /tmp/stargate.lock -c '/usr/local/qcloud/stargate/admin/start.sh > /dev/null 2>&1 &'
0 0 * * * "/root/.acme.sh"/acme.sh --cron --home "/root/.acme.sh" > /dev/null
```

## 6. 关于修改ReloadCmd

目前修改`ReloadCmd`没有专门的命令，可以通过重新安装证书来实现修改`reloadCmd`的目的。 此外，安装证书后，相关信息是保存在`~/.acme.sh/example.com/example.conf`文件下的，内容就是`acme.sh --info -d example.com`输出的信息，不过`ReloadCmd`在文件中使用了Base64编码。理论上可以通过直接修改该文件来修改`ReloadCmd`，且修改时，无需Base64编码，直接写命令原文`acme.sh`也可以识别。 不过，`example.conf`文件的位置和内容格式以后可能会改变！`example.conf`一直都是内部使用, 后面有可能会改为用 sqlite 或者mysql 格式存储. 所以一般不建议自己修改。

## 7. 更新 acme.sh

目前由于 acme 协议和 letsencrypt CA 都在频繁的更新, 因此 acme.sh 也经常更新以保持同步.

升级 acme.sh 到最新版 :

```
acme.sh --upgrade
```

如果你不想手动升级, 可以开启自动升级:

```
acme.sh --upgrade --auto-upgrade
```

之后, acme.sh 就会自动保持更新了.

你也可以随时关闭自动更新:

```
acme.sh --upgrade --auto-upgrade  0
```

## 8. 出错怎么办：

如果出错, 请添加 debug log：

```
acme.sh --issue  .....  --debug 
```

或者：

```
acme.sh --issue  .....  --debug  2
```