title: php  rtrim 汉字
date: 2015-12-17 16:53:38
categories: PHP #文章文类
tags: [PHP]
---

总结下我在`php rtrim` 遇到的转汉字的问题.

<!--more-->

```php
$str = "曲阜机场";
echo rtrim($str,'机场');
```

发现结果竟然乱码

转化成十六进制终于发现了问题

执行如下代码
```java
echo urlencode("曲阜机场").'</br>';
echo urlencode("机场").'</br>';
echo urlencode(rtrim($str,'机场')).'</br>';
```
结果为
```java
%E6%9B%B2%E9%98%9C%E6%9C%BA%E5%9C%BA
%E6%9C%BA%E5%9C%BA
%E6%9B%B2%E9%98
```

原来问题是这样的机场的十进制码为` %E6 %9C %BA %E5 %9C %BA` ，然后曲阜的最后一个十进制编码有个%9C,所以也被过滤了。
这既是问题的本质，所以说，不能用rtrim来过滤中文。

那么可以用如下方法替换
```java
$str = "曲阜机场";
echo preg_replace('/(机场){1}$/iu', '',$str);
```
其实正则表达式的功能真的很强大

