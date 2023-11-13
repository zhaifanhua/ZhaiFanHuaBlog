---
title: 为VisualStudio新建文件创建版权日期模板
date: 2022-04-15 01:43:05
updated: 2022-04-15 01:43:05
description: 为VisualStudio新建文件创建版权日期模板
keywords: VisualStudio
cover: https://cdn.zhaifanhua.com/blog/img/202204160322428.png
top_img: https://cdn.zhaifanhua.com/blog/img/202204160322428.png
tags:
  - VisualStudio
  - CSharp
  - DotNet
categories:
  - 教程
---





# 1.为什么创建模板文件？

在没有创建模板文件时，VisualStudio默认创建的文件是这样的。

![image-20220416030359492](https://cdn.zhaifanhua.com/blog/img/202204160322427.png)

可以看到，没有任何注释、版权信息和创建日期等。而在创建模板文件后，VisualStudio默认创建的文件是这样的。

![image-20220416030832732](https://cdn.zhaifanhua.com/blog/img/202204160322428.png)

这在后续修改代码文件或多人合作项目时有莫大的好处。下面就来说说怎么创建并修改默认模板。

# 2.新建模板文件

> 注意：我所用环境为`Visual Studio 2022`，以下模板适合`C#10`新语法，旧语法及旧版本以类似方法修改。

## 2.1在空白目录创建以下三个文件：

![image-20220416031825018](https://cdn.zhaifanhua.com/blog/img/202204160322429.png)

Class.cs

``` c#
// ----------------------------------------------------------------
// Copyright ©$year$ ZhaiFanhua All Rights Reserved.
// FileName:$safeitemname$
// Guid:$guid1$
// Author:$username$
// Email:me@zhaifanhua.com
// CreateTime:$time$
// ----------------------------------------------------------------

namespace $rootnamespace$;
/// <summary>
/// $safeitemrootname$
/// </summary>
public class $safeitemrootname$
{
}
```

Controller.cs `这里仅为ApiController`

``` c#
// ----------------------------------------------------------------
// Copyright ©$year$ ZhaiFanhua All Rights Reserved.
// FileName:$safeitemname$
// Guid:$guid1$
// Author:$username$
// Email:me@zhaifanhua.com
// CreateTime:$time$
// ----------------------------------------------------------------

using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;

namespace $rootnamespace$;
/// <summary>
/// $safeitemrootname$
/// </summary>
[Route("api/[controller]")]
[ApiController]
public class $safeitemname$ : ControllerBase
{
}
```

Interface.cs

``` c#
// ----------------------------------------------------------------
// Copyright ©$year$ ZhaiFanhua All Rights Reserved.
// FileName:$safeitemname$
// Guid:$guid1$
// Author:$username$
// Email:me@zhaifanhua.com
// CreateTime:$time$
// ----------------------------------------------------------------

namespace $rootnamespace$;
/// <summary>
/// $safeitemrootname$
/// </summary>
public interface $safeitemrootname$
{
}
```

## 2.2找到模板目录并复制

例如安装的Visual Studio 2022在C盘，则对应的模板目录在：

【Class】

```
C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\ItemTemplates\CSharp\Code\2052\Class
C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\ItemTemplates\AspNetCore\Code\1033\Class
```

【Interface】

```
C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\ItemTemplates\CSharp\Code\2052\Interface
C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\ItemTemplates\AspNetCore\Code\1033\Interface
```

【Controller】

```
C:\Program Files\Microsoft Visual Studio\2022\Community\Common7\IDE\ItemTemplates\AspNetCore\Web\ASP.NET\1033\WebApiEmptyController
```

