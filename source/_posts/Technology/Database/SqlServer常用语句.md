---
title: SqlServer常用语句及函数
date: 2021-07-12 11:14:05
updated: 2021-12-30 10:36:05
description: SqlServer常用语句及函数
keywords: SqlServer
tags:
  - SqlServer
categories:
  - SqlServer
---







# 一、SqlServer常用语句

## 1、删除所有表

### 1.1、删除所有表的外键约束（防止由于外键约束表删除失败）

```sql
DECLARE c1 cursor for
select 'alter table ['+ object_name(parent_obj) + '] drop constraint ['+name+']; '
from sysobjects
where xtype = 'F'
open c1
declare @c1 varchar(8000)
fetch next from c1 into @c1
while(@@fetch_status=0)
begin
exec(@c1)
fetch next from c1 into @c1
end
close c1
deallocate c1
```

### 1.2、删除所有的存储过程

```sql
use 数据库名(是要删除表所在的那个数据库的名称)
GO
declare @sql varchar(8000)
while (select count(*) from sysobjects where type='P')>0
begin
SELECT @sql='drop Procedure ' + name
FROM sysobjects
WHERE (type = 'P')
ORDER BY 'drop Procedure ' + name
exec(@sql)
end
```

 1.3、删除所有表

```sql
use 数据库名(是要删除表所在的那个数据库的名称)
GO
declare @sql varchar(8000)
while (select count(*) from sysobjects where type='U')>0
begin
SELECT @sql='drop table ' + name
FROM sysobjects
WHERE (type = 'U')
ORDER BY 'drop table ' + name
exec(@sql)
end
```





# 二、SqlServer常用函数

## 1、concert() 函数

> concert() 函数是把日期转换为新数据类型的通用函数。concert() 函数可以用不同的格式显示日期/时间数据。

### 1.1、语法

```
CONVERT(data_type(length),data_to_be_converted,style)
```

*data_type(length)* 规定目标数据类型（带有可选的长度）。*data_to_be_converted* 含有需要转换的值。*style* 规定日期/时间的输出格式。

可以使用的 *style* 值：

| Style ID    | Style 格式                            |
| :---------- | :------------------------------------ |
| 100 或者 0  | mon dd yyyy hh:miAM （或者 PM）       |
| 101         | mm/dd/yy                              |
| 102         | yy.mm.dd                              |
| 103         | dd/mm/yy                              |
| 104         | dd.mm.yy                              |
| 105         | dd-mm-yy                              |
| 106         | dd mon yy                             |
| 107         | Mon dd, yy                            |
| 108         | hh:mm:ss                              |
| 109 或者 9  | mon dd yyyy hh:mi:ss:mmmAM（或者 PM） |
| 110         | mm-dd-yy                              |
| 111         | yy/mm/dd                              |
| 112         | yymmdd                                |
| 113 或者 13 | dd mon yyyy hh:mm:ss:mmm(24h)         |
| 114         | hh:mi:ss:mmm(24h)                     |
| 120 或者 20 | yyyy-mm-dd hh:mi:ss(24h)              |
| 121 或者 21 | yyyy-mm-dd hh:mi:ss.mmm(24h)          |
| 126         | yyyy-mm-ddThh:mm:ss.mmm（没有空格）   |
| 130         | dd mon yyyy hh:mi:ss:mmmAM            |
| 131         | dd/mm/yy hh:mi:ss:mmmAM               |

### 1.2、实例

下面的脚本使用 concert() 函数来显示不同的格式。我们将使用 GETDATE() 函数来获得当前的日期/时间：

```
CONVERT(VARCHAR(19),GETDATE())
CONVERT(VARCHAR(10),GETDATE(),110) 
CONVERT(VARCHAR(11),GETDATE(),106)
CONVERT(VARCHAR(24),GETDATE(),113)
```

结果类似：

```
Dec 29 2008 11:45 PM
12-29-2008
29 Dec 08
29 Dec 2008 16:25:46.635
```

## 2、count()函数

### 2.1、count(1) and count(*)

当表的数据量大些时，对表作分析之后，使用count(1)还要比使用count( * )用时多了！ 
从执行计划来看，count(1)和count( * )的效果是一样的。 但是在表做过分析之后，count(1)会比count( * )的用时少些（1w以内数据量），不过差不了多少。 如果count(1)是聚索引,id,那肯定是count(1)快。但是差的很小的。 
因为count( * ),自动会优化指定到那一个字段。所以没必要去count(1)，用count( * )，sql会帮你完成优化的 因此：count(1)和count( * )基本没有差别！ 

### 2.2、count(1) and count(字段)

两者的主要区别是
（1） count(1) 会统计表中的所有的记录数，包含字段为null 的记录。
（2） count(字段) 会统计该字段在表中出现的次数，忽略字段为null 的情况。即不统计字段为null 的记录。 

### 2.3、count(*) 和 count(1)和count(列名)区别  

执行效果上：
count(*)包括了所有的列，相当于行数，在统计结果的时候，不会忽略列值为NULL
count(1)包括了忽略所有列，用1代表代码行，在统计结果的时候，不会忽略列值为NULL
count(列名)只包括列名那一列，在统计结果的时候，会忽略列值为空（这里的空不是只空字符串或者0，而是表示null）的计数，即某个字段值为NULL时，不统计。

执行效率上：
列名为主键，count(列名)会比count(1)快
列名不为主键，count(1)会比count(列名)快
如果表多个列并且没有主键，则 count（1） 的执行效率优于 count（ * ）
如果有主键，则 select count（主键）的执行效率是最优的 
如果表只有一个字段，则 select count（*）最优。

## 3、len() 函数

len() 函数返回文本字段中值的长度。

## 4、cast() 函数

4.1、CAST()函数的参数是一个表达式，它包括用AS关键字分隔的源值和目标数据类型。以下例子用于将文本字符串'12'转换为整型:

```
SELECT CAST('12' AS int)
```

4.2、返回值是整型值12。如果试图将一个代表小数的字符串转换为整型值，又会出现什么情况呢？

```
SELECT CAST('12.5' AS int)  
```

4.3、CAST()函数和CONVERT()函数都不能执行四舍五入或截断操作。由于12.5不能用int数据类型来表示，所以对这个函数调用将产生一个错误:

```
Server: Msg 245, Level 16, State 1, Line 1 Syntax error converting the varchar value     '12.5' to a column of data type int.
```

4.4、要返回一个合法的数值，就必须使用能处理这个值的数据类型。对于这个例子，存在多个可用的数据类型。如果通过CAST()函数将这个值转换为decimal类型，需要首先定义decimal值的精度与小数位数。在本例中，精度与小数位数分别为9 与2。精度是总的数字位数，包括小数点左边和右边位数的总和。而小数位数是小数点右边的位数。这表示本例能够支持的最大的整数值是9999999，而最小的小数是0.01。

```
SELECT CAST('12.5' AS decimal(9,2))
```

decimal数据类型在结果网格中将显示有效小数位: 12.50

4.5、精度和小数位数的默认值分别是18与0。如果在decimal类型中不提供这两个值，SQL Server将截断数字的小数部分，而不会产生错误。

```
SELECT CAST('12.5' AS decimal)
```

结果是一个整数值：12



# 三、常用问题

## Sqlserver数据库还原一直显示“正在还原…”解决方法

```sql
RESTORE database 数据库名  with recovery
```

