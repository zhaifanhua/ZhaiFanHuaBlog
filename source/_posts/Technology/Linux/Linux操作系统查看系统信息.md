---
title: Linux操作系统查看系统信息
date: 2021-09-02 18:14:05
updated: 2021-09-02 18:15:05
description: Linux操作系统查看系统信息
keywords: Linux
tags:
  - Linux
categories:
  - 服务器
---



### 1、CPU信息

Linux下的CPU信息全部都在/proc/cpuinfo这个文件中，可以直接打开看。

总核数 = 物理CPU个数 X 每颗物理CPU的核数
总逻辑CPU数 = 物理CPU个数 X 每颗物理CPU的核数 X 超线程数

判断是否开启了超线程：
如果多个逻辑CPU的"physical id"和"core id"均相同，说明开启了超线程
或者换句话说：
逻辑CPU个数 > 物理CPU个数 * CPU内核数 开启了超线程
逻辑CPU个数 = 物理CPU个数 * CPU内核数 没有开启超线程

（1）查看物理CPU的个数

```
 cat /proc/cpuinfo |grep "physical id"|sort |uniq|wc -l
 # 或
 grep "physical id" /proc/cpuinfo|sort -u
 # 或
 grep 'physical id' /proc/cpuinfo | sort -u | wc –l
```

（2）查看每个物理CPU中core的个数(即核数)

```
  cat /proc/cpuinfo| grep "cpu cores"| uniq
  # 或
  cat /proc/cpuinfo |grep "cores"|uniq
  # 或
  grep 'core id' /proc/cpuinfo | sort -u | wc -l
```

（3）查看逻辑CPU的个数

```
 cat /proc/cpuinfo| grep "processor"| wc -l
```

（4）查看线程数

```
grep 'processor' /proc/cpuinfo | sort -u | wc -l
```

（5）查看CPU信息（型号）

```
  cat /proc/cpuinfo | grep name | cut -f2 -d: | uniq –c
```

（6）查看各个物理CPU上面封装的逻辑处理器（即超线程后的CPU）个数

```
cat /proc/cpuinfo | grep siblings
```

（7）查看CPU的主频

```
cat /proc/cpuinfo |grep MHz|uniq
```

（8）linux CPU大小

```
cat /proc/cpuinfo |grep "model name" && cat /proc/cpuinfo |grep "physical id"
```

说明：Linux下可以在/proc/cpuinfo中看到每个cpu的详细信息。但是对于双核的cpu，在cpuinfo中会看到两个cpu。常常会让人误以为是两个单核的cpu。其实应该通过Physical Processor ID来区分单核和双核。而Physical Processor ID可以从cpuinfo或者dmesg中找到。flags 如果有 ht 说明支持超线程技术。判断物理CPU的个数可以查看physical id 的值，相同则为同一个物理CPU。
如：

```
  model name: Intel(R) Xeon(TM) CPU 2.80GHz
  model name: Intel(R) Xeon(TM) CPU 2.80GHz
  model name: Intel(R) Xeon(TM) CPU 2.80GHz
  model name: Intel(R) Xeon(TM) CPU 2.80GHz
  physical id : 0
  physical id : 0
  physical id : 3
  physical id : 3
```
可以看到上面，这台机器有两个双核的CPU，ID分别是0和3，大小是2.8G。

### 2、查看内存信息

```
cat /proc/meminfo
```

### 3、 内存大小

```
cat /proc/meminfo |grep MemTotal
```

### 4、硬盘大小

```
fdisk -l |grep Disk
```

### 5、查看主板型号

```
dmidecode |grep -A16 "System Information$"
```

### 6、查看机器型号

```
dmidecode | grep "Product Name"
```

### 7、查看当前操作系统内核信息

```
uname -a
```

### 8、查看当前操作系统发行版信息

```
cat /etc/issue | grep Linux
```

### 9、 更多查看linux硬件信息的方法

```
uname -a # 查看内核/操作系统/CPU信息的linux系统信息命令
head -n 1 /etc/issue # 查看操作系统版本，是数字1不是字母L
cat /proc/cpuinfo # 查看CPU信息的linux系统信息命令
hostname # 查看计算机名的linux系统信息命令
lspci -tv # 列出所有PCI设备
lsusb -tv # 列出所有USB设备的linux系统信息命令
lsmod # 列出加载的内核模块
env # 查看环境变量资源
free -m # 查看内存使用量和交换区使用量
df -h # 查看各分区使用情况
du -sh # 查看指定目录的大小
grep MemTotal /proc/meminfo # 查看内存总量
grep MemFree /proc/meminfo # 查看空闲内存量
uptime # 查看系统运行时间、用户数、负载
cat /proc/loadavg # 查看系统负载磁盘和分区
mount | column -t # 查看挂接的分区状态
fdisk -l # 查看所有分区
swapon -s # 查看所有交换分区
hdparm -i /dev/hda # 查看磁盘参数(仅适用于IDE设备)
dmesg | grep IDE # 查看启动时IDE设备检测状况网络
ifconfig # 查看所有网络接口的属性
iptables -L # 查看防火墙设置
route -n # 查看路由表
netstat -lntp # 查看所有监听端口
netstat -antp # 查看所有已经建立的连接
netstat -s # 查看网络统计信息进程
ps -ef # 查看所有进程
top # 实时显示进程状态用户
w # 查看活动用户
id # 查看指定用户信息
last # 查看用户登录日志
cut -d: -f1 /etc/passwd # 查看系统所有用户
cut -d: -f1 /etc/group # 查看系统所有组
crontab -l # 查看当前用户的计划任务服务
chkconfig –list # 列出所有系统服务
chkconfig –list | grep on # 列出所有启动的系统服务程序
rpm -qa # 查看所有安装的软件包
cat /proc/cpuinfo # 查看CPU相关参数的linux系统命令
cat /proc/partitions # 查看linux硬盘和分区信息的系统信息命令
cat /proc/meminfo # 查看linux系统内存信息的linux系统命令
cat /proc/version # 查看版本，类似uname -r
cat /proc/ioports # 查看设备io端口
cat /proc/interrupts # 查看中断
cat /proc/pci # 查看pci设备的信息
cat /proc/swaps # 查看所有swap分区的信息
```

