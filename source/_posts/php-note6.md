title: 基于jenkins和phing的php持续集成环境搭建
date: 2015-12-27 12:30:47
categories: PHP
tags: [PHP]
---
### 介绍

本文主要介绍怎么用`jenkins`和`phing`搭建php的持续集成环境，通过这个环境你可以得到你php应用的一系列产物，比如，开发文档，部署包，代码静态分析等等。而且你还是很方便的得到他们，其实这一切都是手动编写的phing tasks.
<!--more-->
通过jenkins以及phing的组合我们可以很方面的执行phing的tasks,而且还可以通过邮件实时的告知结果等等。

首先，给大家提供一些有用的连接，大家可以参考下：
- [示例代码地址](https://github.com/xiabin/Ci-Php-Phing-Example)
- [Jenkins CI Server]([http://jenkins-ci.org/):jenkins官网
- [Phing](https://www.phing.info/trac/):用来构建和管理项目
- [PHPUnit](https://phpunit.de/):单元测试
- [CodeSniffer](http://pear.php.net/package/PHP_CodeSniffer):代码规范检查
- [PHPM](http://phpmd.org/):代码混乱的检查
- [PHPCpd](https://github.com/sebastianbergmann/phpcpd):代码的粘贴和复制检测
- [PHPLoc](https://github.com/sebastianbergmann/phploc):代码结构的概要，代码有多少行，有多少类等等。
- [PHPDepend](http://pdepend.org/):分析不同模块之间的依赖性
- [The DocBlox Project](http://phpdoc.org/):生成API文档
- [Phing user guide](https://www.phing.info/docs/guide/current/):phing用户指南

### 开始
首先你要安装一些软件：
- jenkins([安装手册](https://wiki.jenkins-ci.org/display/JENKINS/Installing+Jenkins))
- PHP
- Ping([安装手册](https://www.phing.info/trac/wiki/Users/Download))
- [Xdebug](http://xdebug.org/)是用来生成单元测得覆盖率报告
- jenkins的[phing插件](https://wiki.jenkins-ci.org/display/JENKINS/Phing+Plugin)

下载示例代码
```java
$ git clone git@github.com:xiabin/Ci-Php-Phing-Example.git  
$ cd Ci-Php-Phing-Example  
```

### 配置buid
[build.properties](https://github.com/xiabin/Ci-Php-Phing-Example/blob/master/build.properties)文件里面定义如何执行工具和生成产物，这个文件被phing的[build.xml](https://github.com/xiabin/Ci-Php-Phing-Example/blob/master/build.xml)文件使用。

在这之前我们应该我们可以先使用默认的配置文件，执行如下命令
```java
Ci-Php-Phing-Example $ cp php.properties.example php.properties  
```
编辑 php.properties然后配置`php.bin`, `php.ini`, `defaultpear.bin`，在你本地的路径。可以参考[示例](https://github.com/xiabin/Ci-Php-Phing-Example/blob/master/resources/php.ini.example)。

然后安装对应的依赖
```java
Ci-Php-Phing-Example $ phing install-dependencies  
```
这个过程可能需要一些时间，他会在根部门创建一个`vendor`，然后安装一些基于pear的指令。如果发现有没有下载下来的，请手动安装在本地，至于为什么会安装在项目里面，就是为了别的人在使用的时候不要去考虑配置的问题，增加可移植性。


### 其他的指令
- `clean`:删除构建build时候生产成的产物目录。
- `report`:根据代码分析工具生成一些报告
- `package`:生成pear和phar的分发包
- `test`:执行单元测试和生产代码覆盖率
- `all`:生成所有报告和执行所有命令

在使用的时候要注意外部和内部vendor目录下的phing指令的问题，最好指定使用哪一个。

### 构建的产物
构建的产物主要在build文件夹下面，它是自动生成的基于build.xml。在每次构建之后你会看到`phar distribution`,`pear package`,`xml`,`html`，这些都是工具自动生成的。

### 工具和软件度量报告
在这个构建环境里面有很多工具是对项目进行静态代码分析，会生成一系列的报告可以发布在jenkins上面：
- Codesniffer: 它会根据用户选择的标准，对用户的代码进行分析，标注出不符合标准的地方。比如该写的注释没有写等等。它对团队之间的代码规范和约束有很大的作用，可以保证团队的成员都使用同样的代码规范。
- PHPMD：是对php代码进行代码混乱程度的检查，会向用户告警一些不符合最佳代码实践的代码。比如某一个类包含过多的方法或者属性。[可以看这个例子](http://phpmd.org/rules/codesize.html)
- PHPCPD：可以分析出项目中任何地方的有粘贴复制嫌疑的代码。
- PDepend:会生成一份项目之间模块之间依赖的情况，包括循环依赖。
- PHPLoc：生成代码行数以及类的数量等代码项目概要的报告。
- DocBlox: 通过代码生成的API文档。

### 包
一共有两种类型的分发包
- Phar file:根据[resources/generatePhar.php.](https://github.com/xiabin/Ci-Php-Phing-Example/blob/master/resources/generatePhar.php)生成。
- Pear package：根据[resources/generatePackageXml.php](https://github.com/xiabin/Ci-Php-Phing-Example/blob/master/resources/generatePackageXml.php)生成。实际上`package.xml`文件的生成是要执行`pear package`pear 命令。

### Resources 目录

[Resources目录](https://github.com/xiabin/Ci-Php-Phing-Example/tree/master/resources)主要是下面一些东西：
- `checkstyle.xsl / cpd.xslt / pdepend.xsl / phpunit_to_surefire.xslt / pmd.xslt`:它们都是和生成一些列可以在jenkins中展示的报告有关的。
- `php.ini.example`:php.ini文件的示例，主要配置了调用工具的相关。
- `generatePhar.php`:生成phar 发行包的简单脚本。
- `generatePackageXml.php`:通过pear生成[package.xml](http://pear.php.net/manual/en/guide.developers.package2.php)的脚本。

### jenkins配置

接下来就是在jenkins里面进行相关的配置，比如触发周期代码获取，安装插件等等。
相关的报告配置可以参考如下：
- `CPD Report`: build/php-cpd/html
- `PHPLoc Report`: build/php-loc/html
- `Coverage Report`: build/php-unit/html
- `PHPDepend dependency graph`: build/php-depend/html
- `PHPDepend pyramid graph`: build/php-depend/html

