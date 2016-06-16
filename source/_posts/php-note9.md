title: php任务管理器 —— Jobby
date: 2016-06-16 20:58:20
categories: PHP
tags: [PHP]
---

一个没有修改crontab的PHP定时任务管理器。
在实战中。php结合crontab是一种经得住历史考验的实践,但是随着项目的不断迭代,计划任务会越来越多,那么相对来说也就难以管理。我们在这里引入一个`任务管理器`。
<!--more-->


## 特性
* 通过一个主crontab任务去维护别的任务
* 自定义的计划任务完全由PHP编写
* 任务的执行计划时间表设置与crontab的时间表设置语法一致
* 在指定的时间内只会运行一个任务
* 邮件告警异常退出任务
* 在root权限下可以控制任务的执行用户
* 指定主机名运行


## 例子

```java
<?php

require_once __DIR__ . '/vendor/autoload.php';

$jobby = new Jobby\Jobby();

// 每一个job都要定义名称
$jobby->add('CommandExample', [
    // 执行shell 命令
    'command'  => 'ls',

    // crontab 时间表语法的支持.
    // 每小时执行一次.
    // 也可以用Y-m-d H:i:s格式的DateTime
    'schedule' => '0 * * * *',

    // Stdout and stderr 指定输出位置
    'output'   => 'logs/command.log',

    // 任务是否有效的开关
    'enabled'  => true,
]);

$jobby->add('ClosureExample', [
    // 执行PHP闭包
    'closure'  => function() {
        echo "I'm a function!\n";
        return true;
    },

    // 每隔两小时执行一次
    'schedule' => '0 */2 * * *',

    'output'   => 'logs/closure.log',
]);

$jobby->run();
```


## 安装

用composer安装:
```java
$ composer require hellogerard/jobby

```

添加master crontab:

```java
* * * * * cd /path/to/project && php jobby.php 1>> /dev/null 2>&1

```

安装结束后,你可以直复制例子到根目录:
```java
$ cp vendor/hellogerard/jobby/resources/jobby.php .

```


每个任务必须的字段:
* `schedule`(string): crontab 时间表语法或者`Y-m-d H:i:s`格式的DateTime
* `command`(sring): shell命令(区别于闭包)
* `closure`(Closure): php匿名函数(区别于命令)


下面的字段可以在每个job里面自定义也可以在`Jobby`的构造函数里面全局定义。
全局定义通常是默认的,在每个job的定义可以覆盖全局的定义

字段         | 类型      | 默认                             | 描述
:------------- | :-------- | :---------------------------------- | :--------------------------------------------------------
runAs          | string    | null                                | 由哪个用户只需, 主crontab任务必须是root用户执行
debug          | boolean   | false                               | 记录 `jobby` 调试信息到'debug.log'
_**Filtering**_|           |                                     | _**决定job是否执行的选项**_
environment    | string    | null or `getenv('APPLICATION_ENV')` | 定义job的执行环境
runOnHost      | string    | `gethostname()`                     | 指定执行job的主机
maxRuntime     | integer   | null                                | job的最大执行时间 (秒)
enabled        | boolean   | true                                | 是否开启job
haltDir        | string    | null                                | 如果目录中有文件则job不执行
_**Logging**_  |           |                                     | _**日志选项**_
output         | string    | /dev/null                           | 重定向 `stdout` and `stderr` 的文件
dateFormat     | string    | Y-m-d H:i:s                         | 格式化`jobby`日志信息的日期格式
_**Mailing**_  |           |                                     | _**错误告警邮件**_
recipients     | string    | null                                | 邮件接受者的地址,逗号分隔`,`
mailer         | string    | sendmail                            | 邮件发送方式: _sendmail_ or _smtp_ or _mail_
smtpHost       | string    | null                                | SMTP host, if `mailer` is smtp
smtpPort       | integer   | 25                                  | SMTP port, if `mailer` is smtp
smtpUsername   | string    | null                                | SMTP user, if `mailer` is smtp
smtpPassword   | string    | null                                | SMTP password, if `mailer` is smtp
smtpSecurity   | string    | null                                | SMTP 安全选项: _ssl_ or _tls_, if `mailer` is smtp
smtpSender     | string    | jobby@&lt;hostname&gt;              | 发件人地址
smtpSenderName | string    | Jobby                               | 发件人名称


## github
[jobby](https://github.com/jobbyphp/jobby)


下次将会分享一个现代化的任务管理器,功能更强大,敬请期待。