title: docker 容器设置分配静态IP以及外网可连接
date: 2015-12-17 13:10:51
categories: Docker #文章文类
tags: [Docker]
---

  在docker使用的时候，是否想过这样两个问题：
  1. 容器如何让外网可以访问
  2. 每次重启容器的时候IP地址都是随机分配是否可以考虑设置成静态的IP
  
  接下来就简单描述下，对上述问题的解决方案。
    <!--more-->

###  解决容器外网访问的问题
   首先要明白docker的网络配置原理
   可以参考国内的文章
   [http://dockerpool.com/static/books/docker_practice/advanced_network/README.html](http://dockerpool.com/static/books/docker_practice/advanced_network/README.html)
   如果你英语好的话可以看官网的介绍
   [https://docs.docker.com/engine/userguide/networking/](https://docs.docker.com/engine/userguide/networking/)
   `本人强烈建议还是去官网看下，其实国内的相关资源很多年都是从国外翻译过来的`
   下面进入正题：
   1. 首先在你的docker宿主机器上面执行`ip a`
      查看的相关信息查看是否存在docker0这个虚拟网卡
      容器使用docker0与宿主主机进行通信；当创建一个容器时，也同时创建一对接口；一个在容器中取名为eth0，另一个在宿主主机中的名称以veth开头；所有的veth*接口都桥接到docker0，这样docker就创建一个在宿主主机和容器间的虚拟网络：
   2. 执行`brctl show`桥接信息(brctl请自行安装）
   > bridge name bridge id       STP enabled interfaces
    docker0     8000.9e071b6066ec   no      veth2d4f9ed
   3. `service docker stop` 停止docker服务
   4. 执行`ip link set dev docker0 down`,`brctl delbr docker0` 删除已经创建的虚拟网卡
   5. 新建一个虚拟网卡br0,并把宿主主机的eth0加入进进去（其实也就是加入真实的网卡，毕竟我们建的是虚拟网卡还是要依赖真实的网卡的）
   ```java
   # brctl addbr br0  //新建网桥br0
   # ip link set dev br0 up //启动这个网桥
   # ip addr add 192.168.15.234/24 dev br0  //给网桥添加IP地址
   # ip addr del 192.168.15.234/24 dev eth0 //删除eth0ip
   # brctl addif br0 eth0 //让eth0成为br0的一个端口
   # ip route del default //删除默认的路由表，这里要注意了 先执行ip route 看下默认的网关是多少 我的是192.168.15.1
   # ip route add default via 192.168.15.1 dev br0 //在这里添加默认路由
   # service networking restart //重启networking
   ```
   **注意上面的使用的IP地址其实就是你eth0原来绑定的ip地址，只不过现在是换到网桥上面去了**
   6. 绑定docker的默认网桥为这个新建的br0
   ```java
   # echo DOCKER_OPTS="-b=br0" >> /etc/default/docker
   # service docker start
   ```
   7. 新建一个容器并查看它的ip地址
   ```java
   # docker run -d -name php php:apahce
   ```
   执行`docker exec -it php bash`进入容器然后执行`ip a`查看具体的ip信息这个时候就会发现
   容器的ip地址和我们宿主机器的IP地址在一个网段上面，那么我们就可以通过外网去访问了。
   
### 为容器分配静态IP
title: docker 容器设置分配静态IP以及外网可连接
date: 2015-12-17 13:10:51
categories: Docker #文章文类
tags: [Docker]
---

  在docker使用的时候，是否想过这样两个问题：
  1. 容器如何让外网可以访问
  2. 每次重启容器的时候IP地址都是随机分配是否可以考虑设置成静态的IP
  
  接下来就简单描述下，对上述问题的解决方案。
    <!--more-->

###  解决容器外网访问的问题
   首先要明白docker的网络配置原理
   可以参考国内的文章
   [http://dockerpool.com/static/books/docker_practice/advanced_network/README.html](http://dockerpool.com/static/books/docker_practice/advanced_network/README.html)
   如果你英语好的话可以看官网的介绍
   [https://docs.docker.com/engine/userguide/networking/](https://docs.docker.com/engine/userguide/networking/)
   `本人强烈建议还是去官网看下，其实国内的相关资源很多年都是从国外翻译过来的`
   下面进入正题：
   1. 首先在你的docker宿主机器上面执行`ip a`
      查看的相关信息查看是否存在docker0这个虚拟网卡
      容器使用docker0与宿主主机进行通信；当创建一个容器时，也同时创建一对接口；一个在容器中取名为eth0，另一个在宿主主机中的名称以veth开头；所有的veth*接口都桥接到docker0，这样docker就创建一个在宿主主机和容器间的虚拟网络：
   2. 执行`brctl show`桥接信息(brctl请自行安装）
   > bridge name bridge id       STP enabled interfaces
    docker0     8000.9e071b6066ec   no      veth2d4f9ed
   3. `service docker stop` 停止docker服务
   4. 执行`ip link set dev docker0 down`,`brctl delbr docker0` 删除已经创建的虚拟网卡
   5. 新建一个虚拟网卡br0,并把宿主主机的eth0加入进进去（其实也就是加入真实的网卡，毕竟我们建的是虚拟网卡还是要依赖真实的网卡的）
   ```java
   # brctl addbr br0  //新建网桥br0
   # ip link set dev br0 up //启动这个网桥
   # ip addr add 192.168.15.234/24 dev br0  //给网桥添加IP地址
   # ip addr del 192.168.15.234/24 dev eth0 //删除eth0ip
   # brctl addif br0 eth0 //让eth0成为br0的一个端口
   # ip route del default //删除默认的路由表，这里要注意了 先执行ip route 看下默认的网关是多少 我的是192.168.15.1
   # ip route add default via 192.168.15.1 dev br0 //在这里添加默认路由
   # service networking restart //重启networking
   ```
   **注意上面的使用的IP地址其实就是你eth0原来绑定的ip地址，只不过现在是换到网桥上面去了**
   6. 绑定docker的默认网桥为这个新建的br0
   ```java
   # echo DOCKER_OPTS="-b=br0" >> /etc/default/docker
   # service docker start
   ```
   7. 新建一个容器并查看它的ip地址
   ```java
   # docker run -d -name php php:apahce
   ```
   执行`docker exec -it php bash`进入容器然后执行`ip a`查看具体的ip信息这个时候就会发现
   容器的ip地址和我们宿主机器的IP地址在一个网段上面，那么我们就可以通过外网去访问了。
   
### 为容器分配静态IP
   如果你可以完成上面的操作，那么下面的操作就简单了。
   1. 首先还是得要有我们的自定义网桥，其实我们也可以在docker0的基础上面进行修改，其实原理都是一样的。
   
   2. 在启动容器的时候添加`-net 参数=none`,比如
   `docker run -d -name php -net=none php:apache`
    
   3. 得到pid
    ```java
    docker inspect -f '{{.State.Pid}}' php
    ```
   比如pid=2456
   4. 执行下面的命令 设置宿主机的虚拟接口
   
   ```java
   mkdir -p /var/run/netns
   ln -s /proc/2456/ns/net /var/run/netns/2456
   ip link add A type veth peer name B
   brctl addif br0 A
   ip link set A up
   ```
   5. 设置容器的虚拟接口
   ```java
   ip link set B netns 2456
   ip netns exec 2456 ip link set dev B name eth0
   ip netns exec 2456 ip link set eth0 up
   ip netns exec 2456 ip addr add 192.168.15.13/24 dev eth0 //这个地址是你想分配的地址
   ip netns exec 2456 ip route add default via 192.168.15.233  //网关的地址设置成br0的ip
   ```
   
   好了这个时候你进入的时候就可以看到IP是你想要的IP了。
      
   但是有个问题就是在容器的重新启动的时候还要重新设置。
   
   其实docker已经有了更便捷的工具去完成这个操作，可以去了解下`pipework`
   [https://github.com/jpetazzo/pipework](https://github.com/jpetazzo/pipework)
   
   
   
   
   