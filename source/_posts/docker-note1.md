  title: Docker常用命令
  date: 2015-11-21 20:13:07
  categories: Docker #文章文类
  tags: [Docker]
---
  
  ### 常用docker命令记录
  

  1. 查看容器的root用户密码
  `docker logs <容器名orID> 2>&1 | grep '^User: ' | tail -n1`
  
  1. 查看容器日志
  `docker logs -f <容器名orID>`
  
  1. 查看正在运行的容器
  `docker ps`
  `docker ps -a为查看所有的容器，包括已经停止的。`
  
  1. 删除所有容器
  `docker rm $(docker ps -a -q)`
     删除单个容器
  `docker rm <容器名orID>`
  
  1. 停止、启动、杀死一个容器
  `docker stop <容器名orID>`
  `docker start <容器名orID>`
  `docker kill <容器名orID>`
    <!--more-->
    
  1. 查看所有镜像
  `docker rmi $(docker images | grep none | awk '{print $3}' | sort -r)`
  
  1. 运行一个新容器，同时为它命名、端口映射、文件夹映射。以redmine镜像为例
  `docker run --name redmine -p 9003:80 -p 9023:22 -d -v /var/redmine/files:/redmine/files -v /var/redmine/mysql:/var/lib/mysql sameersbn/redmine`
  
  1. 一个容器连接到另一个容器
  `docker run -i -t --name sonar -d -link mmysql:db   tpires/sonar-server
   sonar`
    容器连接到mysql容器，并将mysql容器重命名为db。这样，sonar容器就可以使用db的相关的环境变量了。
    
  1. 拉取镜像
  `docker pull <镜像名:tag>`
  `docker pull sameersbn/redmine:latest`
  
  1. 当需要把一台机器上的镜像迁移到另一台机器的时候，需要保存镜像与加载镜像。
  机器a
  `docker save busybox-1 > /home/save.tar`
  使用scp将save.tar拷到机器b上，然后：
  `docker load < /home/save.tar`
  
  1. 构建自己的镜像
  `docker build -t <镜像名> <Dockerfile路径>`
  如Dockerfile在当前路径：
  `docker build -t xx/gitlab .`
  
  1. 后台运行(-d)、并暴露端口(-p)
`docker run -d -p 127.0.0.1:33301:22 centos6-ssh`

  1. 从container中拷贝文件出来
 `sudo docker cp 7bb0e258aefe:/etc/debian_version`
 拷贝7bb0e258aefe中的/etc/debian_version到当前目录下。
 *注意：只要7bb0e258aefe没有被删除，文件命名空间就还在，可以放心的把exit状态的container的文件拷贝出来*
 
  1. 详细点的介绍
    + 镜像管理
    ```java
    docker images：列出本地所有镜像
    docker search <IMAGE_ID/NAME>：查找image
    docker pull <IMAGE_ID>：下载image
    docker push <IMAGE_ID>：上传image
    docker rmi <IMAGE_ID>：删除image
    ```
     + 容器管理
     ```java
    docker run -i -t <IMAGE_ID> /bin/bash：-i：标准输入给容器    -t：分配一个虚拟终端    /bin/bash：执行bash脚本
    -d：以守护进程方式运行（后台）
    -P：默认匹配docker容器的5000端口号到宿主机的49153 to 65535端口
    -p <HOT_PORT>:<CONTAINER_PORT>：指定端口号
    - -name： 指定容器的名称
    - -rm：退出时删除容器
    
    docker stop <CONTAINER_ID>：停止container
    docker start <CONTAINER_ID>：重新启动container
    docker ps - Lists containers.
    -l：显示最后启动的容器
    -a：同时显示停止的容器，默认只显示启动状态
    
    docker attach <CONTAINER_ID> 连接到启动的容器
    docker logs <CONTAINER_ID>  : 输出容器日志
    -f：实时输出
    docker cp <CONTAINER_ID>:path hostpath：复制容器内的文件到宿主机目录上
    docker rm <CONTAINER_ID>：删除container
    docker rm `docker ps -a -q`：删除所有容器
    docker kill `docker ps -q`
    docker rmi `docker images -q -a`
    docker wait <CONTAINER_ID>：阻塞对容器的其他调用方法，直到容器停止后退出
    
    docker top <CONTAINER_ID>：查看容器中运行的进程
    docker diff <CONTAINER_ID>：查看容器中的变化
    docker inspect <CONTAINER_ID>：查看容器详细信息（输出为Json）
    -f：查找特定信息，如docker inspect -f '{{ .NetworkSettings.IPAddress }}'
          docker commit -m "comment" -a "author" <CONTAINER_ID>  ouruser/imagename:tag
    
          docker extc -it <CONTAINER> <COMMAND>：在容器里执行命令，并输出结果
     ```
      + 网络管理
      ```java
      docker run -P：随机分配端口号
      docker run -p 5000:5000：绑定特定端口号（主机的所有网络接口的5000端口均绑定容器的5000端口）
      docker run -p 127.0.0.1:5000:5000：绑定主机的特定接口的端口号
      docker run -d -p 127.0.0.1:5000:5000/udp training/webapp python app.py：绑定udp端口号
      docker port <CONTAINER_ID> 5000：查看容器的5000端口对应本地机器的IP和端口号
      使用Docker Linking连接容器：
      Docker为源容器和接收容器创建一个安全的通道，容器之间不需要暴露端口，接收的容器可以访问源容器的数据
      docker run -d -P --name <CONTAINER_NAME> --link <CONTAINER_NAME_TO_LINK>:<ALIAS>  
      ```
      + 数据管理
      `Data Volumes：volume`是在一个或多个容器里指定的特殊目录
      数据卷可以在容器间共享和重复使用
      可以直接修改容器卷的数据
      容器卷里的数据不会被包含到镜像中
      容器卷保持到没有容器再使用它
      可以在容器启动的时候添加-v参数指定容器卷，也可以在Dockerfile里用VOLUMN命令添加
      `docker run -d -P --name web -v /webapp training/webapp python app.py`
      也可以将容器卷挂载到宿主机目录或宿主机的文件上，<容器目录或文件>的内容会被替换为<宿主机目录或文件>的内容，默认容器对这个目录有可读写权限
      `docker run -d -P --name web -v <宿主机目录>:<容器目录> training/webapp python app.py`
      可以通过指定ro，将权限改为只读
      `docker run -d -P --name web -v <宿主机目录>:<容器目录>:ro training/webapp python app.py`
      在一个容器创建容器卷后，其他容器便可以通过--volumes-from共享这个容器卷数据，如下：
      `docker run -d -v /dbdata --name db1 training/postgres echo Data-only container for postgres`
      首先启动了一个容器，并为这个容器增加一个数据卷/dbdata，然后启动另一个容器，共享这个数据卷
      `docker run -d --volumes-from db1 --name db2 training/postgres`
      此时db2使用了db1的容器卷，当容器db1被删除时，容器卷也不会被删除，只有所有容器不再使用此容器卷时，才会被删除
      docker rm -v：删除容器卷
      除了共享数据外，容器卷另一个作用是用来备份、恢复和迁移数据
      `docker run --volumes-from db1 -v /home/backup:/backup ubuntu tar cvf /backup/backup.tar /dbdata`
      启动一个容器数据卷使用db1容器的数据卷，同时新建立一个数据卷指向宿主机目录/home/backup，将/dbdata目录的数据压缩为/backup/backup.tar
      `docker run -v /dbdata --name dbdata2 ubuntu /bin/bash`
      `docker run --volumes-from dbdata2 -v /home/backup:/backup busybox tar xvf /backup/backup.tar`
      启动一个容器，同时把backup.tar的内容解压到容器的backup



#### 入门的这些够了，想要深入学习请参考以下资料:
* **Docker官方主页**
[http://www.docker.com/](http://www.docker.com/)
* **Docker Hub**
[http://hub.docker.com](http://hub.docker.com)
* **Docker官方博客**
[http://blog.docker.com/](http://blog.docker.com/)
* **Docker官方文档**
[http://docs.docker.com/](http://docs.docker.com/)
* **Docker快速入门指南**
[http://www.docker.com/tryit/](http://www.docker.com/tryit/)
* **Docker的Github源代码**
[https://github.com/docker/docker](https://github.com/docker/docker)
* **Docker Forge(收集了各种Docker工具、组件和服务)**
[https://github.com/dockerforge](https://github.com/dockerforge)
* **Docker邮件列表**
[https://groups.google.com/forum/#!forum/docker-user](https://groups.google.com/forum/#!forum/docker-user)
* **Docker的Twitter主页**
[http://twitter.com/docker](http://twitter.com/docker)
* **Docker的StackOverflow问答主页**
[http://stackoverflow.com/search?q=docker](http://stackoverflow.com/search?q=docker)










