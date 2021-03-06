title: mysql语句的优化原则
date: 2015-12-25 10:16:20
categories: Mysql #文章文类
tags: [Mysql]
---
数据库的优化主要包括两个方面：
- sql程序语句的优化
- 数据库服务器和配置的优化

关于查询语句的优化主要涉及两个方面：一些普通遵循的规则，以及怎样对查询语句进行性能分析。

<!--more-->

### 基本语句优化的10个原则

#### 1. 尽量避免在列上面进行运算，这样会导致索引失败。

#### 2. 使用join的时候应该用小结集去驱动大结果集。同时把负责的join查询拆分为多个query。

#### 3.like模糊查询使用的时候经量避免%%，如果实在是要使用可以考虑建立全文索引表。
 
#### 4. select * 或者列出需要的字段，这样其实是对速度没有影响的，主要考虑的是节省内存。

#### 5.使用批量插入语句节省交互
eg: 
```java
insert into table(key,value) values(1,2);
insert into table(key,value) values(3,4);
insert into table(key,value) values(5,6);
insert into table(key,value) values(7,8);
```
可以优化为
```java
insert into table(key,value) values(1,2),(3,4),(5,6);
```

#### 6. limit 的基数比较大的时候使用between，between字段最好使用int之类的类型
比如像时间之类的 存储int类型在使用between肯定比datetime的时候快

#### 7. 不要使用rand随机函数获取多条随机记录
建议可以使用php或者类似的语言去产生随数然后生成sql去执行

#### 8. 避免使用null
应该指定列为NOT NULL，除非你想存储NULL。在MySQL中，含有空值的列很难进行查询优化，而且对表索引时不会存储NULL值的，所以如果索引的字段可以为NULL，索引的效率会下降很多。因为它们使得索引、索引的统计信息以及比较运算更加复杂。你应该用0、一个特殊的值或者一个空串代替空值

#### 9. 不要使用count(id)，而应该使用count(*)

count(某个字段)的时候数据库只能去安装这个字段去寻找优化的空间，而count(*)的时候数据库可以自动去找可以优化的字段去优化。也就是我们把数据库的优化交给数据库

#### 10. mysql不要经常使用order by，使用的时候一定要做索引优化，或者在索引中完成排序。

## 总结
上面说的就是我们在实战中可以直接采纳的东西，具体的还需要我们去深入的体会，去深入的了解mysql。