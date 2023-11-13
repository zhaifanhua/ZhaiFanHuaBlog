---
title:  SQL游标(cursor)详细说明及内部循环使用示例
date: 2022-06-10 19:59:05
updated: 2022-06-10 20:15:05
description:  SQL游标(cursor)详细说明及内部循环使用示例
keywords: 游标,cursor,SQL
tags:
  - 数据库
categories:
  - 数据库
---



# SQL游标(cursor)详细说明及内部循环使用示例

## 游标

> 游标（cursor）是系统为用户开设的一个数据缓冲区，存放SQL语句的执行结果。每个游标区都有一个名字,用户可以用SQL语句逐一从游标中获取记录，并赋给主变量，交由主语言进一步处理。


游标是处理结果集的一种机制，它可以定位到结果集中的某一行，多数据进行读写，也可以移动游标定位到你所需要的行中进行操作数据。一般复杂的存储过程，都会有游标的出现，他的用处主要有：

1. 定位到结果集中的某一行。
2. 对当前位置的数据进行读写。
3. 可以对结果集中的数据单独操作，而不是整行执行相同的操作。
4. 是面向集合的数据库管理系统和面向行的程序设计之间的桥梁。

## 优点

> 在数据库中，游标是一个十分重要的概念。游标提供了一种对从表中检索出的数据进行操作的灵活手段，就本质而言，游标实际上是一种能从包括多条数据记录的结果集中每次提取一条记录的机制。游标总是与一条SQL 查询语句相关联因为游标由结果集（可以是零条、一条或由相关的选择语句检索出的多条记录）和结果集中指向特定记录的游标位置组成。当决定对结果集进行处理时，必须声明一个指向该结果集的游标。如果曾经用C 语言写过对文件进行处理的程序，那么游标就像您打开文件所得到的文件句柄一样，只要文件打开成功，该文件句柄就可代表该文件。对于游标而言，其道理是相同的。可见游标能够实现按与传统程序读取平面文件类似的方式处理来自基础表的结果集，从而把表中数据以平面文件的形式呈现给程序。我们知道关系数据库管理系统实质是面向集合的，在MS SQL SERVER 中并没有一种描述表中单一记录的表达形式，除非使用where 子句来限制只有一条记录被选中。因此我们必须借助于游标来进行面向单条记录的数据处理。由此可见，游标允许应用程序对查询语句select 返回的行结果集中每一行进行相同或不同的操作，而不是一次对整个结果集进行同一种操作；它还提供对基于游标位置而对表中数据进行删除或更新的能力；而且，正是游标把作为面向集合的数据库管理系统和面向行的程序设计两者联系起来，使两个数据处理方式能够进行沟通。

## 缺点

> 游标速度较慢。

## 种类

> MS SQL SERVER 支持三种类型的游标：Transact_SQL 游标，API服务器游标和客户游标。

（1）*Transact_SQL 游标*

Transact_SQL 游标是由DECLARE CURSOR 语法定义、主要用在Transact_SQL脚本、存储过程和触发器中。Transact_SQL 游标主要用在服务器上，由从客户端发送给服务器的Transact_SQL 语句或是批处理、存储过程、触发器中的Transact_SQL 进行管理。 Transact_SQL 游标不支持提取数据块或多行数据。


（2）*API游标*

API 游标支持在OLE DB， ODBC 以及DB_library 中使用游标函数，主要用在服务器上。每一次客户端应用程序调用API 游标函数，MS SQL SEVER 的OLE DB 提供者、ODBC驱动器或DB_library 的动态链接库（DLL） 都会将这些客户请求传送给服务器以对API游标进行处理。

（3）*客户游标*

客户游标主要是当在客户机上缓存结果集时才使用。在客户游标中，有一个缺省的结果集被用来在客户机上缓存整个结果集。客户游标仅支持静态游标而非动态游标。由于服务器游标并不支持所有的Transact-SQL语句或批处理，所以客户游标常常仅被用作服务器游标的辅助。因为在一般情况下，服务器游标能支持绝大多数的游标操作。由于API 游标和Transact-SQL 游标使用在服务器端，所以被称为服务器游标，也被称为后台游标，而客户端游标被称为前台游标。在本章中我们主要讲述服务器（后台）游标。

## 分类

> 根据游标检测结果集变化的能力和消耗资源的情况不同，SQL Server支持的API服务器游标分为一下4种：

- *静态游标* : 静态游标的结果集，在游标打开的时候建立在TempDB中，不论你在操作游标的时候，如何操作数据库，游标中的数据集都不会变。例如你在游标打开的时候，对游标查询的数据表数据进行增删改，操作之后，静态游标中select的数据依旧显示的为没有操作之前的数据。如果想与操作之后的数据一致，则重新关闭打开游标即可。
- *动态游标* : 这个则与静态游标相对，滚动游标时，动态游标反应结果集中的所有更改。结果集中的行数据值、顺序和成员在每次提取时都会变化。所有用户做的增删改语句通过游标均可见。如果使用API函数或T-SQL Where Current of子句通过游标进行更新，他们将立即可见。在游标外部所做的更新直到提交时才可见。
- *只进游标*：只进游标不支持滚动，只支持从头到尾顺序提取数据，数据库执行增删改，在提取时是可见的，但由于该游标只能进不能向后滚动，所以在行提取后对行做增删改是不可见的。
- *键集驱动游标*：打开键集驱动游标时，该有表中的各个成员身份和顺序是固定的。打开游标时，结果集这些行数据被一组唯一标识符标识，被标识的列做删改时，用户滚动游标是可见的，如果没被标识的列增该，则不可见，比如insert一条数据，是不可见的，若可见，须关闭重新打开游标。 静态游标在滚动时检测不到表数据变化，但消耗的资源相对很少。动态游标在滚动时能检测到所有表数据变化，但消耗的资源却较多。键集驱动游标则处于他们中间，所以根据需求建立适合自己的游标，避免资源浪费。

## 生命周期

> 游标的生命周期包含有五个阶段：声明游标、打开游标、读取游标数据、关闭游标、释放游标。

### 1，声明游标

```sql
DECLARE cursor_name CURSOR [ LOCAL | GLOBAL ] 
     [ FORWARD_ONLY | SCROLL ] 
     [ STATIC | KEYSET | DYNAMIC | FAST_FORWARD ] 
     [ READ_ONLY | SCROLL_LOCKS | OPTIMISTIC ] 
     [ TYPE_WARNING ] 
     FOR select_statement 
     [ FOR UPDATE [ OF column_name [ ,...n ] ] ]
```

参数说明：

- cursor_name：游标名称。

- Local：作用域为局部，只在定义它的批处理，存储过程或触发器中有效。

- Global：作用域为全局，由连接执行的任何存储过程或批处理中，都可以引用该游标。

- Local | Global: 默认为local。

- Forward_Only:指定游标智能从第一行滚到最后一行。Fetch Next是唯一支持的提取选项。如果在指定Forward_Only是不指定Static、KeySet、Dynamic关键字，默认为Dynamic游标。如果Forward_Only和Scroll没有指定，Static、KeySet、Dynamic游标默认为Scroll，Fast_Forward默认为Forward_Only

- Static:静态游标

- KeySet：键集游标

- Dynamic：动态游标，不支持Absolute提取选项

- Fast_Forward：指定启用了性能优化的Forward_Only、Read_Only游标。如果指定啦Scroll或For_Update，就不能指定他啦。

- Read_Only:不能通过游标对数据进行删改。

- Scroll_Locks：将行读入游标是，锁定这些行，确保删除或更新一定会成功。如果指定啦Fast_Forward或Static，就不能指定他啦。

- Optimistic：指定如果行自读入游标以来已得到更新，则通过游标进行的定位更新或定位删除不成功。当将行读入游标时，sqlserver不锁定行，它改用timestamp列值的比较结果来确定行读入游标后是否发生了修改，如果表不行timestamp列，它改用校验和值进行确定。如果已修改改行，则尝试进行的定位更新或删除将失败。如果指定啦Fast_Forward,则不能指定他。

- Type_Warning:指定将游标从所请求的类型隐式转换为另一种类型时向客户端发送警告信息。

- For Update[of column_name ,....]:定义游标中可更新的列。

### 2，声明一个动态游标

```sql
declare orderNum_02_cursor cursor scroll
for select OrderId from bigorder where orderNum='ZEORD003402'
```

### 3，打开游标

```sql
--打开游标语法
open [ Global ] cursor_name | cursor_variable_name
```

cursor_name:游标名，cursor_variable_name：游标变量名称，该变量引用了一个游标。

```sql
--打开游标
open orderNum_02_cursor
```

### 4，提取数据

```sql
--提取游标语法
Fetch
[ [Next|prior|Frist|Last|Absoute n|Relative n ]
from ]
[Global] cursor_name
[into @variable_name[,....]]
```

 

参数说明：

- Frist：结果集的第一行
- Prior：当前位置的上一行
- Next：当前位置的下一行
- Last：最后一行
- Absoute n：从游标的第一行开始数，第n行。
- Relative n：从当前位置数，第n行。
- Into @variable_name[,...] : 将提取到的数据存放到变量variable_name中。

例子：

```sql
--提取数据
fetch first from orderNum_02_cursor
fetch relative 3 from orderNum_02_cursor
fetch next from orderNum_02_cursor
fetch absolute 4 from orderNum_02_cursor
fetch next from orderNum_02_cursor
fetch last from orderNum_02_cursor 
fetch prior from orderNum_02_cursor
select * from bigorder where orderNum='ZEORD003402'
```

例子：

```sql
--提取数据赋值给变量
declare @OrderId int
fetch absolute 3 from orderNum_02_cursor into @OrderId
select @OrderId as id
select * from bigorder where orderNum='ZEORD003402'
```


通过检测全局变量**@@Fetch_Status**的值，获得提取状态信息，该状态用于判断Fetch语句返回数据的有效性。当执行一条Fetch语句之后，@@Fetch_Status可能出现3种值：

- **0**，Fetch语句成功。
- **-1**：Fetch语句失败或行不在结果集中。
- **-2**：提取的行不存在。

这个状态值可以帮你判断提取数据的成功与否。

```sql
declare @OrderId int
fetch absolute 3 from orderNum_02_cursor into @OrderId
while @@fetch_status=0  --提取成功，进行下一条数据的提取操作
 begin
   select @OrderId as id
   fetch  next from orderNum_02_cursor into @OrderId  --移动游标
 end
```

### 5.利用游标更新删除数据

```sql
--游标修改当前数据语法
Update 基表名 Set 列名=值[,...] Where Current of 游标名
--游标删除当前数据语法
Delete 基表名  Where Current of 游标名
```

游标更新删除当前数据

```sql
--1.声明游标
declare orderNum_03_cursor cursor scroll
for select OrderId ,userId from bigorder where orderNum='ZEORD003402'
--2.打开游标
open orderNum_03_cursor
--3.声明游标提取数据所要存放的变量
declare @OrderId int ,@userId varchar(15)
--4.定位游标到哪一行
fetch First from orderNum_03_cursor into @OrderId,@userId  --into的变量数量必须与游标查询结果集的列数相同
while @@fetch_status=0  --提取成功，进行下一条数据的提取操作 
 begin
   if @OrderId=122182
     begin
     Update bigorder Set UserId='123' Where Current of  orderNum_03_cursor  --修改当前行
     end
   if @OrderId=154074
      begin
      Delete bigorder Where Current of  orderNum_03_cursor  --删除当前行
      end
   fetch next from orderNum_03_cursor into @OrderId ,@userId  --移动游标
 end
```

### 6，关闭游标

游标打开后，服务器会专门为游标分配一定的内存空间存放游标操作的数据结果集，同时使用游标也会对某些数据进行封锁。所以游标一旦用过，应及时关闭，避免服务器资源浪费。

```sql
--关闭游标语法
close [ Global ] cursor_name | cursor_variable_name
--关闭游标
close orderNum_03_cursor
```

### 7，删除游标

删除游标，释放资源

```sql
--释放游标语法
deallocate  [ Global ] cursor_name | cursor_variable_name
--释放游标
deallocate orderNum_03_cursor
```

使用实例：

```sql
USE Test_DB;

DECLARE @jid CHAR(5)
DECLARE @pic NVARCHAR(64)
DECLARE My_Cursor CURSOR --定义游标
FOR (SELECT jid FROM journal WHERE isall in(1,2)) --查出需要的集合放到游标中
OPEN My_Cursor; --打开游标
FETCH NEXT FROM My_Cursor INTO @jid; --读取第一行数据
WHILE @@FETCH_STATUS = 0
    BEGIN
        SET @pic=(SELECT TOP 1 smallpic FROM journalissue WHERE jid=@jid and (smallpic != '' and smallpic is not null) ORDER BY issueyear DESC,issueno DESC);
        PRINT (@jid +'    '+ @pic);
        IF(@jid != '' and @jid is not null and @pic != '' and @pic is not null)
        BEGIN
            UPDATE journal SET pic=@pic WHERE jid=@jid;
        END        
        FETCH NEXT FROM My_Cursor INTO @jid; --读取下一行数据
    END
CLOSE My_Cursor; --关闭游标
DEALLOCATE My_Cursor; --释放游标
GO
```

详细说明
RS.OPEN SQL,CONN,A,B
参数A为设定游标的类型，其取值为： 0 仅向前游标，只能向前浏览记录，不支持分页、Recordset、BookMark
1 键集游标，其他用户对记录所做的修改将反映到记录集中，但其他用户增加或删除记录不会反映到记录集中。支持分页、Recordset、BookMark
2 动态游标功能最强，但耗资源也最多。用户对记录所做的修改，增加或删除记录都将反映到记录集中。支持全功能浏览。
3 静态游标，只是数据的一个快照，用户对记录所做的修改，增加或删除记录都不会反映到记录集中。支持向前或向后移动
参数B为记录集的锁定类型，其取值为：
1 锁定类型，默认的，只读，不能作任何修改
2 当编辑时立即锁定记录，最安全的方式
3 只有在调用Update方法时才锁定记录集，而在此前的其他操作仍可对当前记录进行更改、插入和删除等
4 当编辑时记录不会被锁定，而更改、插入和删除是在批处理方式下完成的
打开数据记录集方法其实不止一种，但是我们用的最多的就是
rs.open sql,1,1的方法，可是后面的数字参数很多人不解其意，下面我们来介绍一下。
其实open方法后面有多个参数
CursorType LockType CommandType
比如 rs.open sql,1,1
也可以写成
rs.cursorType = 1
rs.LockType = 1
rs.open sql
其中CursorType代表从一个表或者一个SQL查询结果返回的记录。
这个参数有四个值分别是：
adOpenForwardOnly 表示只允许在记录集内的记录间往前移动。这个是缺省值。
adOpenKeyset 反映由其它用户所做的对记录的改变或者删除动作，但并不反映由其它用户做作的添加新记录的动作。
adOpenDynamic 反映由其它用户所做的对记录的改变或者删除动作，包括添加的新记录
adOpenStatic 不反映其它用户对记录所做的修改，添加，删除动作。
这四个值VBSCRIPT预定义位
adOpenForwardOnly = 0
adOpenKeyset = 1
adOpenDynamic = 2
adOpenStatic = 3
lockType 表示当打开记录集时，数据提供者用于锁定数据库的类型：
adLockReadOnly 数据不能改变，这是缺省值！
adLockPessimistic 数据提供者在开始编辑数据的时候锁定记录
adLockOptimistic 仅当调用update方法时，数据提供者锁定记录
adLockBatchOptimistic 用于批处理修改
他们的常量值定义分别是：
adLockReadOnly = 1
adLockPessimistic = 2
adLockOptimistic = 3
adLockBatchOptimistic = 4
rs.open sql,conn,1,1 读取记录 select
rs.open sql,conn,1,3 只更新记录最好 update
rs.open sql,conn,2,3 插入和删除最好 insert delete

示例：

```sql
 1 USE [Community];
 2 
 3 DECLARE @UserInfoID bigint,@Name nvarchar(100),@UCAccountID bigint;
 4 
 5 
 6 DECLARE My_Cursor CURSOR --定义游标
 7 FOR (
 8     select distinct(UI.[UserInfoID]),UI.[Name],UIToUC.[UCAccountID] from [dbo].[CMAreaRelation] as AR 
 9         inner join [dbo].[UserInfo] as UI on UI.[UserInfoID]=AR.[Creator]
10         inner join [dbo].[UserInfoToUCAccount] as UIToUC on UIToUC.[UserInfoID]=UI.[UserInfoID]
11 ) --查出需要的集合放到游标中
12 OPEN My_Cursor; --打开游标
13 FETCH NEXT FROM My_Cursor INTO @UserInfoID,@Name,@UCAccountID; --读取第一行数据
14 WHILE @@FETCH_STATUS = 0
15     BEGIN
16         PRINT '@UserInfoID='+convert(varchar,isnull(@UserInfoID,0))+'， @Name='+isnull(@Name,'')+'， @UCAccountID='+convert(varchar,isnull(@UCAccountID,0)); --打印，方便查看（正式项目不需要该行）
17         --这里是根据每一行编写自定义的操作……
18 
19         FETCH NEXT FROM My_Cursor INTO @UserInfoID,@Name,@UCAccountID; --读取下一行数据
20     END
21 CLOSE My_Cursor; --关闭游标
22 DEALLOCATE My_Cursor; --释放游标
23 GO
```
