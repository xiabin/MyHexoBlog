title: php页面乱码问题
date: 2015-12-24 15:25:22
categories: PHP #文章文类
tags: [PHP]
---
主要记录下php页面乱码问题的解决办法，以及讨论下问题的本质。

<!--more-->

## 1.如果页面里面没有说明编码情况那么就会使用apache的默认设置。
    apache的`default_charset`设置。
    页面设置是说在html里面用` <meta http-equiv="Content-Type" content="text/html; charset=utf-8" />`的方式设定。
## 2.页面指定charset为utf-8, Apache配置`default_charset` gbk. 页面文件是utf-8.
    当Apache配置了default_charset, 将忽略页面的charset申明。
## 3.Apache设置`default_charset` off
   页面显示正常。
## 4.PHP header申明charset为utf8, Apache配置`default_charset` gbk,页面文件编码是utf8

## 总结
   也就是说，当apache不指定default_charset的时候，页面编码由页面自己的meta标签指定。
   当apache指定的时候，将忽略页面中的meta标签指定的编码. 但是容许脚本直接header编码方式给客户端
