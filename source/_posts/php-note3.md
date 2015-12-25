title: php的生命周期
date: 2015-12-23 11:18:41
categories: PHP #文章文类
tags: [PHP]
---
这里将会记录我对php生命周期的理解.
<!--more-->

作为php的开发人员，我们接触php最多的场景就是在web的环境下面，比如php作为apache的一个模块加载进去，或者是以fastcgi模式运行php。
除了web模式之前的php还有CLI模式的。其实不管在什么模式下面，php的工作原理都是一样的。都是在作为一种SAPI在运行。

### SAPI
这是对SAPI的英文解释`Server Application Programming Interface： the API used by PHP to interface with Web Servers`我们可以认为SAPI就是PHP对外部的一个代理或者对外接口吧，它把外部环境抽象后, 为内部的PHP提供一套固定的, 统一的接口, 使得PHP自身实现能够不受错综复杂的外部环境影响，保持一定的独立性。这个是对外的下面我们去看下php的内部情况。

### PHP的启动与终止
php程序启动的时候有两个概念上的启动
- 模块的启动
- 请求的启动
同样每个请求对应着终止
- 模块的终止
- 请求的终止

举个例子：php作为apache模块时候的，`模块的启动`是伴随着apache的启动而启动的，这个时候会初始化一些与apache有管或者php自身需要初始化的信息；然后当apache为php分配一个请求的时候就会有一次`请求的启动`。这一种是我们在实战中最常见的一种。

在php扩展里面我们可以看到这样四个方法，大家不妨可以自己编写程序看下具体是怎么启动的
`PHP_MINIT_FUNCTION`
`PHP_RINIT_FUNCTION`
`PHP_RSHUTDOWN_FUNCTION`
`PHP_MSHUTDOWN_FUNCTION`
这里我们说的只是php中典型的一种启动模式。
当然一个PHP实例，无论通过http请求调用的，还是从命令行启动的，都会向我们上述那样， 依次进行Module init、Request init、Request Shutdown、Module shutdown四个过程。唯一不同就是这两种模块执行的次数，还记得之前说的SAPI吗，这主要就是取决与PHP是与哪一种SAPI通信的。
我们常见的四种方式如下：
- 直接以CLI/CGI模式调用
- 多进程模式
- 多线程模式
- Embedded(嵌入式，在自己的C程序中调用Zend Engine)


### CLI/CGI
这种模式php的生命周期是在一个单独请求中完成，也就是说每次执行都是把上述所说的模块执行一次。
### 多进程模式
多进程模式的特点就是没起到一个进程则调用一次Module模块。每处理一次请求就调用一次请求模块。其实apache就是每次fork多个进程去处理php的请求。
### 多线程模式
在这种模式下，只有一个服务器进程在运行着，但会同时运行很多线程，这样可以减少一些资源开销， 像Module init和Module shutdown就只需要运行一次就行了，一些全局变量也只需要初始化一次， 因为线程独具的特质，使得各个请求之间方便的共享一些数据成为可能。
### Embed 
这种方式我们应该比较少见，mebed模式独特的是因为它可能随时嵌入到某个程序里面去， 然后被当作脚本的一部分在一个请求的时候执行。

## 总结
上面就简单地介绍下我对php生命周期的理解


