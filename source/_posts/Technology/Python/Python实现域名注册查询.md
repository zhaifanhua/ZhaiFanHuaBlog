---
title: Python实现域名注册查询
date: 2020-11-8 11:14:05
updated: 2021-6-15 12:15:05
description: Pythonn实现域名注册查询
keywords: Python,域名注册
cover: https://cdn.zhaifanhua.com/blog/img/20210615232152.png
top_img: https://cdn.zhaifanhua.com/blog/img/20210615232152.png
tags:
  - Python
  - 域名
categories:
  - Python
---



# Python实现域名注册查询

本程序由 [摘繁华](https://blog.zhaifanhua.com) 和好友 [蓝白社区](https://blog.bglb.work/) 联合出品；

本程序使用第三方接口提供服务，不保证持续稳定性，若失效，请留言反馈；

本程序仅供学习交流，请勿在于商业；

## 文件下载

摘繁华私有云盘分享：[文件下载](https://yun.zhaifanhua.com/s/ydFW)

## 文件说明

![image-20210615230641656](https://cdn.zhaifanhua.com/blog/img/20210615230646.png)

> source，源码文件夹；run，运行程序文件夹；readme，文档及截图。

## 运行说明

1. 修改 run 文件夹下 config.json 配置文件为您需要的查询条件；
2. 点击执行程序运行即可；

> config.json配置说明：

| 配置项      | 配置详解     | 配置类型 | 可否为空 | 示例                        |
| ----------- | ------------ | -------- | -------- | --------------------------- |
| domain_list | 域名可选字典 | list     | 是       | ["a","b","c"] 或 []         |
| count       | 域名生成数量 | int      | 否       | 10（注意不能为0）           |
| bits        | 域名长度     | int      | 否       | 5（注意不能为0）            |
| ext         | 域名后缀     | list     | 是       | [".com",".cn",".net"] 或 [] |

## 运行截图

![exe运行截图](https://cdn.zhaifanhua.com/blog/img/20210615232152.png)

## 源码分享

1、main.py

```python
"""
--*-- conding:utf-8 --*--
Author:zhaifanhua
Email:me@zhaifanhua.com
Time:2020.11.08 上午 02:27
"""

import json
import os
import sys
import time
from past.builtins import raw_input
from domain import domain_create
import xmltodict
import requests


def getxml(domain: str):
    url = "http://panda.www.net.cn/cgi-bin/check.cgi"
    res = requests.post(url, {"area_domain": domain})
    return res.content.decode('utf-8')


def xml_to_json(xmldoc):
    try:
        tmp = json.loads(json.dumps(xmltodict.parse(xmldoc, encoding='')))
    except Exception:
        tmp = {
            'property':{
                'returncode': -1,
                'original': '000',
            }
        }
    return tmp["property"]


def parse_out(domain: list):
    path = "./result"
    if not os.path.exists(path):
        os.makedirs(path)
    for key in domain:
        res_dict = xml_to_json(getxml(key))
        if res_dict["returncode"] == "200":
            statu = "请求成功"
        else:
            statu = "请求失败"
        if res_dict["original"][:3] == "210":
            original = "-------域名可以注册--------"

            with open("./result/result_true.txt", "a", encoding="utf8") as result_true:
                result_true.write(key + "\n")

        elif res_dict["original"][:3] == "211":
            original = "域名已经被注册"
        elif res_dict["original"][:3] == "212":
            original = "域名参数传输错误"
        else:
            original = "查询超时"
        out_str = "域名：{0}\t请求状态：{1}\t注册状态：{2}".format(key, statu, original)
        with open("./result/result.txt", "a", encoding="utf8") as result:
            result.write(out_str + "\n")
        time.sleep(2)
        print(out_str)
    raw_input("查询完毕！请在result文件夹下的result_true.txt中查看可购买的域名！感谢您的使用，请按任意键退出此窗口！")


def read_json():
    try:
        with open('./config.json', 'r', encoding="utf8") as c:
            config_json = json.load(c)
    except:
        print("配置文件出错！请检查config.json文件！按任意键退出此窗口")
        time.sleep(8)
        sys.exit(1)
    if config_json["count"] is None or config_json["bits"] is None:
        raw_input("配置文件出错！请检查config.json文件！ 按任意键退出此窗口")

    return config_json


if __name__ == '__main__':
    config = read_json()
    domain_list = domain_create(domain_list=list(config["domain_list"]), count=int(config["count"]),
                                bits=int(config["bits"]), ext=list(config["exts"]))
    parse_out(domain_list)

```

2、domain.py

```python
"""
--*-- conding:utf-8 --*--
Author:zhaifanhua
Email:me@zhaifanhua.com
Time:2020.11.08 上午 02:27
"""

import random


def domain_create(domain_list, count=50, bits=5, ext: list = [".com", ".cn", ".net"]):
    """
            域名生成方法
    :param domain_list:域名可选字典
    :param count:域名生成数量
    :param bits:域名长度
    :param ext:域名后缀
    :return: domain_list
    """
    if len(domain_list) == 0:
        domain_list = [chr(i) for i in range(97, 123)]  # 默认为a到z全字典
    input_str = ""

    input_list = []
    for n in range(count):
        for f in range(bits):
            i = random.randint(0, len(domain_list) - 1)
            input_str += domain_list[i]
        if len(ext) == 0:
            ext = [".com"]
        for e in ext:
            input_list.append(input_str + e)
        input_str = ""
    return input_list


if __name__ == '__main__':
    num_list = ['t', 'u', 'b', 'g', 'l', 'z', 'y', 'q']
    print(domain_create(num_list, 500, 5))
```

3、config.json

```json
{
  "domain_list": [],
  "count": 1000,
  "bits": 6,
  "exts":[".com"]
}
```

## 更新记录

### v1.0.0

#### 感谢：

1、感谢接口提供者；

#### 新增：

1、域名自定义，具体为字典自定义和后缀自定义；

2、域名状态查询；

#### 优化：

1、查询记录保存为文本，方便后续查看，使用者不再需要关注实时信息；

2、可注册域名保存为文本，方便后续查看；

### v1.0.1

#### 感谢：

感谢粉丝 @星星 提出此 bug；

#### 修复：

1、因接口访问规则变化而产生程序异常闪退的问题，已优化为错误提示，不会闪退；

2、因接口爬虫机制变化导致一直请求失败的问题；



> 如果你觉得这篇文章还不错，请动动小指头点赞、收藏和关注哦！若本文带给你很大帮助，也可以打赏博主一杯可乐ღゝ◡╹)ノ♡
> 摘繁华版权所有，转发或引用请附上原文链接哦！