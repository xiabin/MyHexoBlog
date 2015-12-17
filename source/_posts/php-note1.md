title: 5分钟搭建基于WAMP+Pphstorm+Xdebug的开发环境
date: 2015-11-20 22:32:32
categories: PHP #文章文类
tags: [PHP]
---

本文主要介绍在windows环境下面搭建php开发环境，主要用的``wamp``,``phpstorm``,``xdebug``搭建方便好用的php开发环境。
<!--more-->
首先我们要下载相关的软件：
* ## `Wamp`下载安装
  Wamp主要由`Apache`+`Php`+`Mysql`组成，可前往官网下载相关的版本，[官网地址](http://www.wampserver.com/).
  作者由于使用的是64位环境所以下载的是64位版本,如图
  ![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_1.png)
  下载后按照提示正常安装。
  安装完成后桌面上有个快捷方式:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_2.jpg)
  双击后悔自动运行，然后在桌面右下角会有一个绿色的标志代表运行正常:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_3.jpg);
  单机该图表会弹出相关的菜单栏，里面可以进行各个功能的相关配置，在这里就不罗嗦。
* ## 开启`xdebug`
  wamp默认就带有了xdebug在这里你只需要开启即可，点击右下角图标找到`PHP=>PHP extensions` 从里面找到xdebug选项点击。然后在`phpinfo`里面查看是否开启成功。
  也可以直接修改php.ini里面的配置，注意wamp真正的php.ini 是在`path/to/wamp/bin/apachex.x.x/bin`下面
* 安装phpstorm
  [`phpstorm`下载地址](https://www.jetbrains.com/phpstorm/)
  下载安装后在本地新建一个php项目,作者在本地的项目如下: ![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_4.jpg)
  接着点击配置如图:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_5.jpg)
  点击完后出现如下页面,选择`PHP Web Application`![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_6.jpg)
  进入如下页面:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_7.jpg)填写`name`和点击`server`后面的安装设置你的服务器地址
  点击server后出现如下页面:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_8.jpg)点击左上角的`+`号会出现图片右边的界面，填写`name`,`host`,`port`,`debugger`
  `name`是服务器的名字`host`是服务器的地址,`port`和`debuger`分别是端口和调试方式，**这里一定要选`xdebug`**
  然后返回到如下页面:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_9.jpg)填写`Start Url`也就是`/path/to/wamp/www/html/`的相对目录。浏览器建议选择默认浏览器，点击ok那么你就设置好了。

  好的，那么如此犀利的开发环境就搭建好了，简单给大家举一个调试的例子。
  * 在项目中打断点
  * 实时跟踪变量的值
  设置后之后会看到如下页面:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_10.jpg)一个是运行的按钮一个是调试的按钮，我们现在是要调试那么就点击调试的按钮。
  点击后会在默认浏览器打开相应页面，这个时候你可以在相应的代码前面添加断点,代码会停留在断点的位置。如图:![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_11.jpg)可以看到变量的值显示在左下角，代码停留在断点的位置。
  ![](http://7xokym.com1.z0.glb.clouddn.com/php_note1_12.jpg)注意这一排按钮是控制单步执行的，具体怎么操作就不在这里多说了，请自行研究。


  其实xdebug的功能很强大，可以远程调试，debug出详细的变量信息等等。下面留给读者自己去研究[xdebug官网](http://xdebug.org/)

  ------

  再一次感谢您花费时间阅读这篇文章,本文只是简历的入门，希望对你有帮助！祝您在这里阅读、分享愉快！
















