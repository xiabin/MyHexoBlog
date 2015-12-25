title: docker搭建简单的分布式应用
date: 2015-12-25 15:36:58
categories: Docker #文章文类
tags: [Docker]
---

学习了docker之后，尝试利用docker容器搭建简单的分布式服务，为以后搭建docker私有云创建基础。

<!--more-->
具体的搭建模型可以参考下图：

![](http://7xokym.com1.z0.glb.clouddn.com/docker_note3_docker.jpg)

搭建的步骤：
- 先假设你已经安装好docker的环境
- 下载php,mysql,nginx容器
- 先安装好mysql容器mysql容器的IP地址采用静态分配，具体可参考我之前写的文章
- 配置mysql的主从复制，具体可以google，很简单
- 配置好data volume主要映射php代码的地址
- 开启三个php容器然后每个容器都是静态的地址
- 配置nginx容器并开启，负载均衡到这三个php容器上面
 好的大功告成你试着访问下就这个nginx容器的对外路径了。
 
 ## 总结
 本文介绍的比较简单，熟悉docker相关命令的人便可以搭建好，后面还准备研究下docker的第三发工具，以及监控平台等等。