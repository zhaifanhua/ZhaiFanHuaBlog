---
title: 彻底的卸载干净oracle 11g
date: 2022-03-07 18:14:05
updated: 2021-03-07 00:15:05
description: 彻底的卸载干净oracle 11g
keywords: oracle
cover: https://cdn.zhaifanhua.com/blog/img/202203071924697.png
top_img: https://cdn.zhaifanhua.com/blog/img/202203071924697.png
tags:
  - oracle
categories:
  - 教程
---



# 彻底的卸载干净oracle 11g

1.关闭oracle所有的服务。可以在windows的服务管理器中关闭；

![img](https://cdn.zhaifanhua.com/blog/img/202203071924697.png)

2.打开注册表：regedit 打开路径： <找注册表 ：开始->运行->regedit>
  HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\ 
  删除该路径下的所有以oracle开始的服务名称，这个键是标识Oracle在windows下注册的各种服务。

![img](https://cdn.zhaifanhua.com/blog/img/202203071924461.png)


3.打开注册表，找到路径： 
  HKEY_LOCAL_MACHINE\SOFTWARE\ORACLE
  删除该oracle目录，该目录下注册着Oracle数据库的软件安装信息。

![img](https://cdn.zhaifanhua.com/blog/img/202203071925216.png)

4.删除注册的oracle事件日志，打开注册表
  HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\Eventlog\Application
  删除注册表的以oracle开头的所有项目。

![img](https://cdn.zhaifanhua.com/blog/img/202203071925464.png)

5.删除环境变量path中关于oracle的内容。
  鼠标右键右单击“我的电脑-->属性-->高级-->环境变量-->PATH 变量。
  删除Oracle在该值中的内容。注意:path中记录着一堆操作系统的目录，在windows中各个目录之间使用分号（;）隔开的，删除时注意。
  建议：删除PATH环境变量中关于Oracle的值时，将该值全部拷贝到文本编辑器中，找到对应的Oracle的值，删除后，再拷贝修改的串，
             粘贴到PATH环境变量中，这样相对而言比较安全。

​       ![img](https://cdn.zhaifanhua.com/blog/img/202203071925661.png)

![img](https://cdn.zhaifanhua.com/blog/img/202203071925818.png)

6.重新启动操作系统。
          以上1~5个步骤操作完毕后，重新启动操作系统。


7.重启操作系统后各种Oracle相关的进程都不会加载了。这时删除Oracle_Home下的所有数据。
   （Oracle_Home指Oracle程序的安装目录）


8.删除C:\Program Files下oracle目录。
    （该目录视Oracle安装所在路径而定）


9.删除开始菜单下oracle项，如：
   C:\Documents and Settings\All Users\「开始」菜单\程序\Oracle - Ora10g
   不同的安装这个目录稍有不同。
   如果不删除开始菜单下的Oracle相关菜单目录，没关系，这个不影响再次安装Oracle.当再次安装Oracle时，该菜单会被替换。


至此，Windows平台下Oracle就彻底卸载了。 