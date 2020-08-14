---
title: mysql索引及SQL语句优化
date: 2020-08-08 19:20:23
updated:
tags: 
    - mysql
    - SQL索引
categories: 学习笔记
keywords:
description: "Mysql的学习分享"
top_img: https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/7.jpg
comments: 
cover: https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/7.jpg
toc_number:
auto_open:
copyright:
mathjax: 
katex:
aplayer:
highlight_shrink: false
---

## Mysql索引

```mysql
-- mysql文件保存位置
show global variables like "%datadir%";

-- MySQL8.0 以元数据文件、非事务表来存储的文件被删除了，比如：.frm, .par, .trn, .isl, .db.opt等都在MySQL8.0中不存在了用专门的表空间mysql.idb来存储。


# 查看表索引
-- show index from orders;
-- show keys from users;
-- desc users;
-- show create table users;

# 主键索引与普通索引
-- explain select * from orders where cust_id = 10001;
-- explain select * from orders where order_num =20005;

# 添加唯一索引 失败
-- alter table orderitems add unique u_pid(prod_id);

# 添加普通索引
-- alter table users add index in_age(age);
-- explain select * from users where age>25;
# 删除普通索引
-- drop index in_age on users;

# 全文索引
-- explain select * from productnotes where note_text like 'Customer%';

# 联合索引
-- explain select * from orderitems where order_item = 1;
-- explain select * from orderitems where order_num = 20005;
-- explain select * from orderitems where order_item = 1 and order_num = 20005;
```

按照老师提供的文档进行了1000W 条数据的插入，想看看索引到底会占多少的存储空间，然后花了半小时才加到了44W 行，果断放弃了!

---

添加索引前文件大小：

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/image-20200808112138351.png)

查找 age=18 花费时间：

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/image-20200808123622779.png)

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/a1_爱奇艺_爱奇艺.jpg)添加普通索引后文件大小：

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/a33.png)

查找 age=18 花费时间：

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/a4.png)

![](https://cdn.jsdelivr.net/gh/daliymove/tuchuang/img/a2.png)

具体原因可能是优化器的优化选择方面出了问题（原因未知）；

文件内容：共44W行数据；



**注意： drop index 删除索引并不会删除存储空间，我理解的是只是断开了与建立索引的链接，并且，后续假如建立相同名称的索引，是不需要消耗空间的；**

具体如何删除表中索引并释放空间的方法没有查到；



## 书写高质量的Mysql 语句

参考链接：[书写高质量SQL的30条建议](https://mp.weixin.qq.com/s?__biz=Mzg2OTA0Njk0OA==&mid=2247486461&idx=1&sn=60a22279196d084cc398936fe3b37772&chksm=cea24436f9d5cd20a4fa0e907590f3e700d7378b3f608d7b33bb52cfb96f503b7ccb65a1deed&token=1987003517&lang=zh_CN%23rd)

