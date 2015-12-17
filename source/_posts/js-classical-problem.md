title: JS一些经典问题
date: 2014-10-21 21:43:32
categories: JavaScript #文章文类
tags: [JavaScript]
---
#### JS一些经典问题 ####

本文介绍下经常出现的javascript兼容性问题以及解决办法

``遍历数值时对DOM监听事件，索引值始终等于遍历结束后的值``
    处理方法 我们可以添加索引属性，或者采用闭包的方法去处理。  
    <!--more-->
``控制this关键字的指向``
在js中this指针的指向是个让人琢磨不透的东西。例如js的伪协议和内链事件对应this指向的不同伪协议一般指向的是当前标签而内联事件则是指向window对象
``setTimeout和setInterva以及DomNode.onxxx``
改变的是直接调用函数里面this的指向，都是把它们指向window，DomNode.onxxx将直接调用函数里面的this指向DomNode.
解决的代办处可以将处理函数由直接调用编程匿名函数间接调用
另外还可以通过call和apply函数来改变处理函数this的指向。



