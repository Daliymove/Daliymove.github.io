---
title: mysql学习总结
date: 2020-08-05 19:20:23
updated:
tags: 
    - mysql
    - SQL语法
categories: 学习笔记
keywords:
description: "Mysql的学习分享"
top_img: https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/6.jpg
comments: 
cover: https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/6.jpg
toc_number:
auto_open:
copyright:
mathjax: 
katex:
aplayer:
highlight_shrink: false
---
---

## 数据库

数据库就是按照数据结构来组织，存储和管理数据的仓库

数据库特点：

* 对数据进行持久化保存
* 方便数据的存储和查询，速度快，安全，方便
* 可以处理并发访问
* 更加安全的权限管理访问机制

常见的数据库：

Mysql 、oricle 、PostgerSQL 、SQLServer(微软)  关系型数据库
redis 非关系型数据库（数据存储在内存中，读取速度快，键值对的形式存储，不是二维表的形式） 
mongoDB 文档型数据库 也是非关系型数据库   配合上面数据库使用 

## 存储引擎
存储引擎： 对具体数据进行操作，读取表中数据，以及将数据存储进物理内存中；

客户端： 连接 Mysql
服务器： 连接管理、查询缓存、语法解析、查询优化等操作
存储引擎：真实的存取数据

Mysql 支持的存储引擎：

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200729164116.png)

> 我们一般常用的是 InnoDB 与 MyISAM

### InnoDB 与 MyISAM 的区别
> MyISAM是MySQL的默认数据库引擎（5.5版之前）,其提供了⼤量的特性，包括全⽂索引、压缩、空间函数等，但MyISAM不⽀持事务和行级锁，而且最⼤的缺陷就是崩溃后无法安全恢复，所以MySQL 5.5版本后默认的存储引擎修改为InnoDB。

|          |              InnoDB              |           MyISAM            |
| :------: | :------------------------------: | :-------------------------: |
| 事务与锁 |         支持事务和行级锁         |        只支持表级锁         |
| 存储结构 |        两个（.frm .ibd ）        |   三个（.frm .MYI .MYD）    |
|  表主键  |     没有会自动创建6字节主键      |   允许没有任何索引和主键    |
|  表行数  |  未生成最大行数（count效率低）   | 生成最大行数（count效率高） |
|   外键   |               支持               |           不支持            |
| CURD操作 | trancate table（用于删除大量行） |    适用于大量select 操作    |
| 应用场景 |          大型高并发应用          |          小型应用           |

* 表级锁： MySQL中锁定**粒度最大**的⼀种锁，对当前操作的整张表加锁，实现简单，资源消耗也比较少，加锁快，不会出现死锁。其锁定粒度最⼤，触发锁冲突的概率最高，并发度最低， MyISAM和 InnoDB引擎都支持表级锁。 
* 行级锁： MySQL中锁定**粒度最小**的⼀种锁，只针对当前操作的行进行加锁。 行级锁能大大减少数据库操作的冲突。其加锁粒度最小，并发度高，但加锁的开销也最大，加锁慢，会出现死锁。

## mysql 事务

事务：一系列增删改查等操作所组成的一个逻辑执行单元

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200805170325.png)

### 事务的语法

事务开启： start transaction;/ begin;

提交：commit; 使得当前的修改确认

回滚：rollback; 使得当前的修改被放弃

### 事务的ACID特性

* **原子性**

事务是最小的执行单位，事务开启后，操作要么全部成功，要么全部失败；

> 事务开始后所有操作，要么全部做完，要么全部不做，不可能停滞在中间环节。事务执⾏过程中出错， 会回滚到事务开始前的状态，所有的操作就像没有发⽣⼀样。也就是说事务是⼀个不可分割的整体，就像化学中学过的原⼦，是物质构成的基本单位。

* **一致性**

执行事务前后，数据保持一致，多个事务对同一数据的读取结果相同；（理解成数据的总和相同）A+ 则B-

* **隔离性**

并发访问数据库时，⼀个用户的事务不被其他事务所⼲扰，各并发事务 之间数据库是独⽴的；

* **持久性**

⼀个事务被提交之后。它对数据库中数据的改变是持久的，即使数据库发生故障也不应该对其有任何影响； （写入磁盘，除非硬盘损坏）

### 事务并发问题

**丢失修改：**指一个事务读取一个数据时，另一个事务也对这个数据进行了读取，如果两个事务都对此数据进行了修改，那么前者的数据修改将会丢失；

**脏读：**读到了未提交的数据； 事务A读取了事务B更新的数据，然后B回滚操作，那么A读取到的 数据是脏数据。

**不可重复读：**同⼀条命令返回不同的结果集；.事务 A 多次读取同⼀数据，事务 B 在事务A 多次读取的过程中，对数据作了更新并提交，导致事务A多次读取同⼀数据时，结果不⼀致；

**幻读：**重复查询的过程中，数据就发⽣了量的变化（insert， delete）
* 诡异的更新事件：
![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/1-15.jpg)

**不可重复读和幻读区别：** 不可重复读的重点是修改，比如多次读取⼀条记录发现其中某些列的值被修改，幻读的重点在于新增或删除，⽐如多次读取⼀条记录发现记录增多或减少了；

**顺序读：** 隔离的最高级别，事务依次逐个进行，不允许并发执行；

### 事务隔离级别

SQL 标准定义了四个隔离级别：

* READ-UNCOMMITTED(读取未提交)： 最低的隔离级别，允许读取尚未提交的数据变更，可能会导致脏读、幻读或不可重复读。
* READ-COMMITTED(读取已提交)： 允许读取并发事务已经提交的数据，可以阻止脏读，但是幻读或不可重复读仍有可能发生。
* REPEATABLE-READ(可重复读)： 对同一字段的多次读取结果都是一致的，除非数据是被本身事务自己所修改，可以阻止脏读和不可重复读，但幻读仍有可能发生。
* SERIALIZABLE(可串行化)： 最高的隔离级别，完全服从ACID的隔离级别。所有的事务依次逐个执行，这样事务之间就完全不可能产生干扰，也就是说，该级别可以防止脏读、不可重复读以及幻读。

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200805185614.png)

MySQL InnoDB 存储引擎的默认⽀持的隔离级别是 REPEATABLE-READ（可重读）；
mysql 5.7 可通过 `select @@tx_isolation;` 来查看；
mysql 8.0 以上可通过`select @@transaction_isolation;` 来查看；

### 不同的隔离级别的锁的情况
1. 读未提交（RU）: 有行级的锁，没有间隙锁。它与RC的区别是能够查询到未提交的数据。
2. 读已提交（RC）：有行级的锁，没有间隙锁，读不到没有提交的数据。
3. 可重复读（RR）：有行级的锁，也有间隙锁，每次读取的数据都是⼀样的，并且没有幻读的情况，存在诡异更新的情况。
4. 序列化（S）：有行级锁，也有间隙锁，读表的时候，就已经上锁了

### 隐式提交
> 事务中输入DDL(Data Define Language) 定义语句时，如(建库,建表,修改表,索引操作,存储过程,视图**等修改表结构的操作**)，都会将修改的结果进行隐式提交；

### 多版本控制MVCC

MVCC : 指的是一种提高并发的技术。最早的数据库系统，只有读读之间可以并发，读写，写读，写写都要阻塞。引入多版本之后，只有写写之间相互阻塞，其他三种操作都可以并行，这样大幅度提高了InnoDB的并发度。在内部实现中，与Postgres在数据行上实现多版本不同，InnoDB是在undolog中实现的，通过undolog可以找回数据的历史版本。找回的数据历史版本可以提供给用户读(按照隔离级别的定义，有些读请求只能看到比较老的数据版本)，也可以在回滚的时候覆盖数据页上的数据。在InnoDB内部中，会记录一个全局的活跃读写事务数组，其主要用来判断事务的可见性。

## 锁

[Mysql锁机制简单了解](https://blog.csdn.net/qq_34337272/article/details/80611486)

## 大表优化

当MySQL单表记录数过⼤时，数据库的CRUD性能会明显下降，⼀些常⻅的优化措施如下：

* 限定数据的范围

添加限制条件，尽量少使用 * 的查找，除非表数据较少；

* 读/写分离

经典的数据库拆分⽅案，主库负责写，从库负责读；

* 垂直分区

* 数据库分片

MySQL大表优化⽅案：https://segmentfault.com/a/1190000006158186

## 扩展

### mysql 存储过程

 mysql存储：将一段SQL 语句打包成一个方法存储下来，处理速度更快，因为是预编译好的方法体；

**创建过程**

`\d // `修改MySQL默认的语句结尾符 ; 改为 // （因为多条语句中会穿插分号；） 

`create procedure` 创建语句 

BEGIN和END语句⽤来限定存储过程体

```mysql
-- 定义存储过程
\d //
create procedure p1()
begin
set @i=10;
while @i<90 do
insert into users values(null,concat('user:',@i),@i,0);
set @i=@i+1;
end while;
end;
//
\d ;
```

查看存储过程：`show create procedure p1\G`

删除存储过程：`drop procedure p1`

**总结**

* 业务逻辑不要封装在数据库里面,应该由应用程序（ JAVA、Python、PHP）处理。 
* 让数据库只做它擅长和必须做的，减少数据库资源和性能的消耗。 
* 维护困难，大量业务逻辑封装在存储过程中，造成业务逻辑很难剥离出来；动A影响B。 
* 人员也难招聘，因为既懂存储过程，又懂业务的⼈少，使用困难。

 **在电信、银行业、金融饭店以及国企都普遍使用存储过程来熟悉业务逻辑，但在互联网中相对较少。**

### mysql触发器

> * 触发器是MySQL响应写操作(增、删、改)而自动执行的一条或一组定义在BEGIN和END之间的 MySQL语句
>
> * 执行触发器类似于JavaScript中的事件

```mysql
-- 创建触发器
CREATE TRIGGER trigger_name trigger_time trigger_event ON tbl_name FOR EACH ROW trigger_stmt
# 说明：
# trigger_name：触发器名称
# trigger_time:触发时间，可取值：BEFORE或AFTER
# trigger_event：触发事件，可取值：INSERT、UPDATE或DELETE。
# tb1_name：指定在哪个表上
# trigger_stmt：触发处理SQL语句。

-- 查看所有的 触发器
show triggers\G
-- 删除触发器
drop trigger trigger_name;


-- 举例：创建⼀个删除的触发器,在users表中删除数据之前,往del_users表中添加⼀个数据

-- 1,复制当前的⼀个表结构
create table del_users like users;

-- 2,创建 删除触发器 注意在创建删除触发器时,只能在删除之前才能获取到old(之前的)数据
\d //
create trigger deluser before delete on users for each row
begin
insert into del_users values(old.id,old.name,old.age,old.account);
end;
//
\d ;
```

**tips：**

* **在INSERT触发器代码内，可引用一个名为NEW的虚拟表，访问被插入的行**
* **在DELETE触发器代码内，可以引用一个名为OLD的虚拟表，访问被删除的行**
* **在UPDATE触发器代码中，可以引用NEW/ OLD 来访问被删除或被插入的行**

**注意：** **OLD中的值全都是只读的，不能更新；在AFTER DELETE的触发器中无法获取OLD虚拟表**



```mysql
-- 练习：
-- 1.创建⼀个表, users_count ⾥⾯有⼀个 num的字段 初始值为0或者是你当前users表中的count
-- 2,给users表创建⼀个触发器
-- 当给users表中执⾏insert添加数据之后,就让users_count⾥⾯num+1,
-- 当users表中的数据删除时,就让users_count⾥⾯num-1,
-- 想要统计users表中的数据总数时,直接查看 users_count

\d //
create trigger addcount after insert on users for each row
begin
update user_count set num = num+1;
end;
create trigger delcount before delete on users for each row
begin
update user_count set num = num-1;
end;
//
\d ;
```

### mysql 视图

**什么是视图？** 

> * 可以理解成用sql查询语句创建的一个表，用于查看符合查询语句条件的数据；
> * 视图本身不包含数据，返回的数据是通过其他表查询而来
> * 对视图进行增删修改等操作，实际是操作查询的原数据表
> * 
>
> **总结：**视图是对SQL 查询语句的封装，不包含任何数据，具有表的结构体，可以增删改查（操作查询的那个表）

```mysql
-- 创建过程
create view v_table as SQL select...


show tables; -- 可以查看到所有的表和视图
show table status where comment='view'\G -- 只查看当前库中的所有视图

drop view v_table; -- 删除视图
```



>  总结一下：mysql存储相当于是定义一个方法体，mysql触发器是在（增、删、修改）事件发生前/后执行某某操作，mysql视图是封装一段动态的查询语句；

## 索引与优化

### 索引的概述与分类

什么是索引？

* 类似于图书的目录，提高数据检索效率，降低数据库的IO成本 

* 也可以理解成一种快速查找排好序的数据结构

索引分类（按查找效率）：

* 主键索引 ：根据主键建立索引，不允许重复，不为空；

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/a1.png)

* 唯一索引  ：用来建立索引的列的值必须是唯一的，允许为空值

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806154340.png)

* 普通索引：用表中的普通列构建的索引，没有限制

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806154501.png)

* 全文索引：大文本对象的列来构建索引（分池）

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806154808.png)

* 组合索引：用多个列组合构建的索引，多个列中不允许有空值

  组合索引的最左前缀原则，如上图所示，必须从email开始查找，不能直接从phone开始查；

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/a0806.jpg)

### 索引原理

MySQL索引的数据结构：

* 哈希索引（用于memory存储引擎,速度快，散列分布，不支持范围查找与排序）

* B+TREE索引，大部分场景都使用此数据结构进行索引

正常情况下，在mysql中如果不指定索引的类型，那么⼀般是指B+Tree索引（或者B+Tree索引）。

### B 树与B+树比较

下图一个三阶的B树，每一个节点都带有数据，倘若需要范围查找，比如查找1-5中的数据，此时需要遍历9次

数字： 1 2 3 4 5

步数： 3 4 5 7 9

所以使用B树会有以下缺点：

1. 查找效率不均一 
2. 范围查找需要中序遍历（返回上层查找）
3. 每⼀个叶子结点上都带有数据（地址不连续）

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806181409.png)

再来看看B+树

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806183054.png)

B+ 树相对于 B树的优势：

1. 磁盘的读写代价更低：B树是索引与数据一起存储，读深层数据IO次数多，B+ 树索引部分只存索引，相同的块空间存储的索引更多
2. 随机IO次数更少：随机IO指读取地址不连续的数据，相较而言B树的随机IO明显多余B+树，B+树则是顺序IO较多
3. 查询速度更稳定：B+树查询的所有数据都要到叶子节点；

### 聚簇索引与非聚簇索引

用主键建立的索引为主索引，其余为辅助索引（数据和索引分离），主键索引只有一个，辅助索引可以有很多个；

* 聚簇索引：能通过索引找到要查找的数据

* 非聚簇索引：通过索引只能找到索引值和key，如果查找其他数据需要回表（去主键索引中查找数据）

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/2020-08.jpg)

myISAM也使用了B+树的数据结构来存储数据，只是在其叶子节点中只存储了索引和行号，即需要查找数据都要根据行号去对应的文件中查找，这种行为我们称之为 '回行' ；



![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806212553.png)

**总结**

InnoDB 中的主键索引是聚簇索引，其余的辅助索引为非聚簇索引；

MyISAM中的主键索引与辅助索引都是非聚簇索引；

## 慢查询与SQL优化

### 慢查询

即慢查询日志，即用来记录响应时间超过查询阈值的语句；一般除非调优，不需要开启，会影响性能；

慢查询⽇志可用于查找需要很长时间才能执行的查询，因此是优化的候选者。

慢查询语法：

```mysql
-- 查看慢查询配置
show variables like '%slow%';  

-- 查看慢查询时间定义
show variables like 'long_query_time';

-- 设置慢查询的时间定义
set long_query_time=2;

-- 开启慢日志
set global slow_query_log ='ON';
```

通过慢查询日志以及 explain 来对查询进行解析

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806215907.png)

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806220108.png)

建立无用的索引会浪费磁盘空间，虽然可以显著提高查找效率；1000W条数据量大概的存储空间为800M，增加一个主键索引变成了1.2G；过多的索引也会影响写入性能；相对应的维护成本也会提高；

### 索引优化

**适当建立索引**

1. 创建并使用自增数字建立主键索引
2. 经常使用的where字段才建立索引
3. 添加索引尽可能的保持一致性
4. 可考虑联合索引进行索引覆盖

**合理使用索引**

1. 不要在列上使用函数或进行计算：如 select * from news where year(public_time) =2017 或 select * from news where id/100 = 1;
2. 防止两侧数据不一致导致隐式转换

**注意：当查询条件左右两侧类型不匹配的时候会发生隐式转换，隐式转换带来的影响就是可能导致索引失效而进行全表扫描。**

3. like 语句的索引失效问题

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806223133.png)

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/20200806223319.png)

### SQL语句优化

1. 避免嵌套语句（子查询）
2. 避免多表查询（复杂查询简单化）



## mysql 相关文章推荐

[一条SQL语句在MySQL中如何执行的](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485097&idx=1&sn=84c89da477b1338bdf3e9fcd65514ac1&chksm=cea24962f9d5c074d8d3ff1ab04ee8f0d6486e3d015cfd783503685986485c11738ccb542ba7&token=79317275&lang=zh_CN%23rd)

[MySQL高性能优化规范建议](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485117&idx=1&sn=92361755b7c3de488b415ec4c5f46d73&chksm=cea24976f9d5c060babe50c3747616cce63df5d50947903a262704988143c2eeb4069ae45420&token=79317275&lang=zh_CN%23rd)

[腾讯⾯试：⼀条SQL语句执⾏得很慢的原因有哪些？---不看后悔系列](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247485185&idx=1&sn=66ef08b4ab6af5757792223a83fc0d45&chksm=cea248caf9d5c1dc72ec8a281ec16aa3ec3e8066dbb252e27362438a26c33fbe842b0e0adf47&token=79317275&lang=zh_CN%23rd)

[书写高质量SQL的30条建议](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486461&idx=1&sn=60a22279196d084cc398936fe3b37772&chksm=cea24436f9d5cd20a4fa0e907590f3e700d7378b3f608d7b33bb52cfb96f503b7ccb65a1deed&token=1987003517&lang=zh_CN%23rd)

