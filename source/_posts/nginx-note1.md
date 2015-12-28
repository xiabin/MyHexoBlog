title: 对nginx配置文件的理解
date: 2015-12-25 20:58:59
categories: Nginx #文章文类
tags: [Nginx]
---

nginx是一种负载均衡服务器，经常被用来处理高并发的网站。它的最大特色莫过于他对高并发的处理，以及静态内容的缓存。虽然很多人都知道nginx的能力，但是对于新用户来说对于nginx的配置文件来说还是不理解的。本文，主要介绍nginx配置文件的一些结构，以及一些简单的示例。

nginx的配置文件一般安装在`/etc/nginx/nginx.conf`, 或者也有可能安装在`/usr/local/nginx/conf/nginx.conf` 或者 `/usr/local/etc/nginx/nginx.conf`,这里主要说的是在linux环境下。

首先我们要理论nginx配置文件的上下文环境，使用`{ }`关联起来的树形结构。括号里面的东西我们称之为配置文件的上下文。由于`{ }`是一种嵌套的结构，也就是上下文存在嵌套，也可以理解为作用域。里面的配置是可以继承或者覆盖的。子配置可以父配置里面的相同关键字的内容。
<!--more-->
接下来我们主要说下我们在使用nginx时候的常见情况。
### 配置文件的结构

nginx配置文件中主要包括六块：main，events，http，server，location，upstream。

#### main部分
main块是唯一一块没有像下面这样括号括起来的部分。他是直接包含在上下文环境的最外层。我们在这里称呼这个环境为main环境
```java
//这里就是main块

. . .

context {

    . . .

}
```

main块是配置的影响相对来说比较广。主要控制nginx子进程的所属用户/用户组、派生子进程数、错误日志位置/级别、pid位置、子进程优先级、进程对应cpu、进程能够打开的文件描述符数目等。当然这里的配置也有可能被制定的模块所覆盖。

#### Events 部分
events 块是直接被包含在main环境里面。它主要是配置nginx处理连接的方式。events下面由定义一个上下文环境。如下所示
```java
# main context

events {

    # events context
    . . .

}
```

nginx 是通过配置连接处理模型来判断如何处理连接的。使用网络I/O模型，linux系统推荐使用epoll，FreeBSD推荐使用kqueue，还有个指令是，worker_connections：每个worker能够处理的最大连接数，取决于ulimit -n的值。
``java
events {
    use epoll;
    worker_connections 10240;
}
```

#### http部分
是nginx处理http请求的主要配置模块，大多数配置都在这里面进行
```java
# main context

events {
    # events context

    . . .

}

http {
    # main context

    . . .

}
```
可以配置日志文件的默认位置(access_log and error_log),配置对文件异步I/O的操作(aio, sendfile, and directio)配置服务器报错的表现(error_page)。其它的配置还有gzip相关的，长连接相关的配置，网络阻塞的相关配置。除此之外还有各种虚拟主机的配置。

https://www.digitalocean.com/community/tutorials/understanding-the-nginx-configuration-file-structure-and-configuration-contexts
#### server部分

server是嵌套在http模块里面的，而且是允许有多个server模块的。

配置文件中的形式如下：
```java
# main context
http: {

    # http context

    server {

        # first server context

    }

    server {

        # second server context

    }

}
```
由于可以指定多个虚拟主机所以可以允许有多个server的定义。每一个虚拟主机都可以处理与其相对应的请求。
由于nginx里面可能会有多个server块的定义，那么nginx也需要根据相应的算法去做决定。nginx会根据server模块的配置，对每一个连接进行处理，nginx要根据请求的详细信息去决定具体让哪一个 块去处理，与其相关的配置如下：
- listen: 主机监听端口（可以是ip:port格式，默认监听127.0.0.1），如果请求与其想匹配那么nginx可能会选择这个listen所在的server块去处理。
- server_name:主机域名，当nginx活结合listen一起去判断，因为会存在从同一个ip过来但是主机域名不一样的信息。
在这个模块的里面的配置可以覆盖很多在http里面配置的过的指令，比如根目录，压缩等等。当然他也会从http模块里面继承一些配置。

#### Location部分
Location部分是server中对应的目录级别的控制块，可以有多个
常用的语法格式如下：
```java
location match_modifier location_match {

    . . .

}    
```
虽然location部分是在server里里面而且也有多个，但是他和server不一样，location可以反复嵌套。
如下
```java
# main context

server {

    # server context

    location /match/criteria {

        # first location context

    }

    location /other/criteria {

        # second location context

        location nested_match {

            # first nested location

        }

        location other_nested {

            # second nested location

        }

    }

}
```

server块的选择是根据 IP地址以及端口的组合，host名称，以及location块对uri的匹配，通常一个请求就是IP以及端口以及uri的组合。
加入一个客户端的请求是`http://www.example.com/blog`端口是80，那么就是根据www.example,com 和端口80决定哪个server块会被选择。被选择之后，`/blog`部分也算是请求的uri,将会被用来判断哪个location去处理对应的请求。
当然还可以用location指定别名`alias`，以及作为代理，让别的服务器来处理请求。

#### Upstream部分
Upstream部分是nginx做反向代理和负载均衡的配置块，可以有多个,代码如下：
```java
# main context

http {

    # http context

    upstream upstream_name {

        # upstream context

        server proxy_server1;
        server proxy_server2;

        . . .

    }

    server {

        # server context

    }

}
```

#### Mail部分
虽然nginx经常被用来做web或者反向代理服务器，但也可以用来做邮件代理服务器。这个模块主要是用做这个功能。
常用的配置如下：
```java
# main context

events {

    # events context

}

mail {

    # mail context

}
```

#### If部分
这个模块是用来做条件处理的，就像是一些编程语言中的if语句一样。经常在做重写的时候会用到这个语句。我们经常会用来判断是否重写或者返回一定的结果，如下：
```java
# main context

http {

    # http context

    server {

        # server context

        location location_match {

            # location context

            if (test_condition) {

                # if context

            }

        }

    }

}
```

#### Limit_except部分
这个部分主要是用来限制客户端的请求，在location模块里面。比如，你的服务器只想接收post的请求，而且是 每个人都可以读取内容，你就可以用这个配置。

配置如下：
```java
. . .

# server or location context

location /restricted-write {

    # location context

    limit_except GET HEAD {

        # limit_except context

        allow 192.168.1.1/24;
        deny all;
    }
}
```

上面配置的结果就是，任何用户都可以用GET和HEAD这个模式的请求，只有来自ip为`192.168.1.1/24`的子网可以使用别的方法。


好的对于我们常用的模式我就介绍到这里，详细的信息可以去看官网的文档：[http://nginx.org/en/docs/dirindex.html](http://nginx.org/en/docs/dirindex.html)
