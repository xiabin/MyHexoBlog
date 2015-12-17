title: 给程序统一的入口
date: 2014-10-21 21:16:39
categories: JavaScript #文章文类
tags: [JavaScript]
---
``给程序统一的入口``
``window.onload和DOMReady``
存在问题:  
1. js加载混乱，没有纪律性
2. 在DOM节点还没有被载入的时候程序会报错  
<!--more-->
当把js文件放在window.onload事件之后，window对象会在网页元素全部加载完毕之后才会触发onload事件，而此时DOM节点已经生成，则执行程序不会报错。
下面说下window.onload和DOMReady的区别：
前者需要等页面加载完成时才会触发，包括图片，flash等富媒体。DOMReady只是判断页面内所有的DOM节点是否已经全部生成，至于节点的内容是否加载完成，它并不关心。所以后者触发比前者快，特别当页面还有大量的富媒体资源时，后者触发很久后才会触发前者。
但是DOMReady不是原生js里面就有的我们可以通过js的框架库来实现，比如说jquery.
如$(document).ready();
或者你也可以把加载js文件的位置写在最接近</body>标签的地方。