---
title: .NET面试题
date: 2021-06-11 18:14:05
updated: 2021-06-12 00:15:05
description: .NET面试题
keywords: .NET,面试题
cover: https://cdn.zhaifanhua.com/blog/img/20210611235021.png
top_img: https://cdn.zhaifanhua.com/blog/img/20210611235021.png
tags:
  - CSharp
  - .NET
  - 面试题
categories:
  - 面试题
---



# .NET面试题

> 这里总结收集.NET面试题。

![image-20210611235007650](https://cdn.zhaifanhua.com/blog/img/20210611235021.png)

## CSharp

暂无

## SQL Server

1、写出一条 SQL 语句：取出文章表（article）中第 31 到第 40 记录（SQL Server，以自动增长的 id 作为主键，注意：id 可能不是连续的。）

```mssql
SELECT * FROM(
    SELECT id, ROW_NUMBER() OVER(ORDER BY id ASC) AS rowid
    FROM article
) T
WHERE T.rowid BETWEEN 31 and 40 
```



