title: mysql in,exists not in,not exists 区别
date: 2017-03-01 21:32:54
categories: Mysql
tags: [Mysql]
keywords: [mysql in exists]
---
阐释了mysql中in,exists not in,not exists的区别。
<!--more-->

## 建立测试数据  
新建一个`user`表  
```
CREATE TABLE `user` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT,
  `username` varchar(255) NOT NULL,
  `email` varchar(20) NOT NULL,
  `intro` varchar(100) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```
我们生成一些测试数据，测试数据如下：
```
mysql> select * from user;
+----+----------+-------+-------+
| id | username | email | intro |
+----+----------+-------+-------+
|  1 | aaa      | aaa   | NULL  |
|  2 | bbb      | bbb   | bbb   |
|  3 | ccc      | ccc   | ccc   |
+----+----------+-------+-------+
3 rows in set (0.00 sec)
```
##  in 与 exists
### 1. `in`不会对`null`进行处理,`exists`会对`null`进行处理  
`in` 
```
mysql> select * from user where intro in (select intro  from user );
+----+----------+-------+-------+
| id | username | email | intro |
+----+----------+-------+-------+
|  2 | bbb      | bbb   | bbb   |
|  3 | ccc      | ccc   | ccc   |
+----+----------+-------+-------+
2 rows in set (0.00 sec)
```
不返回 intro 为null的数据
`exist`
```
mysql> select * from user where exists  (select intro  from user );
+----+----------+-------+-------+
| id | username | email | intro |
+----+----------+-------+-------+
|  1 | aaa      | aaa   | NULL  |
|  2 | bbb      | bbb   | bbb   |
|  3 | ccc      | ccc   | ccc   |
+----+----------+-------+-------+
3 rows in set (0.00 sec)

```
### 2. 执行的先后顺序以及性能
`in`  
子查询为驱动表，子查询的表先被访问  
`exists`  
主查询为驱动表，主查询的表先被访问  
可以得出：
**如果两个表中一个较小，一个是大表，则子查询表大的用exists，子查询表小的用in。**
如果两个表都差不多一样大的话，效率是差不多的。    
原因：  
`in` 是把外表和内表做[hash join](https://en.wikipedia.org/wiki/Hash_join)，而`exists`是对外表作loop，每次loop再对内表进行查询。每次循环查的时候，和`in`模式不一样的地方是：返回值是真或假，是真就输出。  
可以分析下带`exists`的语句加深下理解  
语句：  
```
select * from user where exists  (select intro  from user );
```
实际执行过程中伪代码如下：
```
for x in ( select * from user ) 
    loop 
       if ( exists ( select intro from user) 
       then 
          OUTPUT THE RECORD 
       end if 
    end loop 

```

##  not in 与 not exists
其实与`in`和`exists`的区别类似,这里不在多说。  
补充:   
`not in` 是使用`anti hash join`进行连接。
 

