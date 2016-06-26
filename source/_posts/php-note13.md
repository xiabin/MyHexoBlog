title: Mac 下php5.3-7.0的二进制包 ── PHP-OSX
date: 2016-06-26 10:53:47
categories: [PHP]
tags: [PHP,Mac]
---

这些安装包主要是安装最新版的 PHP 7.0/5.6/5.5/5.4/5.3 。可适用于与T OS X 10.6 (aka Snow Leopard), OS X 10.7 (aka Lion) and OS X 10.8 (aka Mountain Lion) and OS X 10.9 (aka Mavericks) and OS X 10.10 (aka Yosemite) and OS X 10.11 (aka El Capitan) 。安装的路径为`/usr/local/php5`.它会帮你安装许多常用的扩展,以及该扩展在php.ini下的设置,这些扩展主要是基于[Liip](https://www.liip.ch/en)的开发环境。该环境十分适用在` Symfony 2`框架下。里面有一套符合php最佳实践的php.ini配置。
<!--more-->


## 在线安装
可以通过下载一个shell脚本,并执行安装,如下:

#### PHP 7.0 (Current stable)

```
curl -s http://php-osx.liip.ch/install.sh | bash -s 7.0

```

#### PHP 5.6 (Current stable)
```
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.6

```

#### PHP 5.5 (Old stable)

```
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.5

```
#### PHP 5.4 (End of life)

```
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.4

```

#### PHP 5.3 (End of life)

```
curl -s http://php-osx.liip.ch/install.sh | bash -s 5.3
```

这个脚本会安装packager 位于 `/usr/local/packer` 以及PHP在`/user/local/php5` ,为此我们需要你输入密码。当然不会做一些对你有害的事情。

## Asked & Questions
* #### 为什么执行`php -v`看到的PHP版本不是我们安装的版本了?
`php-osx` 不会覆盖mac下安装的php二进制可执行程序,但是会安装所有东西在`/usr/local/php5`, 因此新的PHP二进制可执行程序在`/usr/local/php5/bin/php`。
你也可以设置你的环境变量包含最新的php程序,比如,在`~/.profile`中加入如下代码:
```
export PATH=/usr/local/php5/bin:$PATH

```

* #### 为什么`Europe/Zurich`是默认的时间?在哪里可以修改?
由于这个包的作者是位于Zurich (and Fribourg and Lausanne)。所以默认的位置是这里。在`/usr/local/php5/php.d/99-liip-developer.ini` 这个文件可以修改。

* #### 为什么不使用用我原有的`php.ini`配置

为了让我们推荐的php.ini设置可以更简单的覆盖原有的php.ini设置,我们引入属于我们这个包的php.ini文件,位于`/usr/local/php5/php.d/99-liip-developer.ini`。
到目前为止,你应该知道这个文件会覆盖其它文件定义的值。当然你也可以删除这个文件。

* #### 为什么mysql socket 配置文件会用`/tmp/mysql.sock`,如何改变?

如果你安装的mysql是从官方网站下载的标准包,那么`/tmp/mysql.sock`是mysql server默认生成的文件。因此我们使用了默认的值,你可以在下面文件中进行修改:
```
sudo /usr/local/php5/bin/pecl install pecl_http

```

* #### `memcached`守护进程在哪?
我们不在包换`memcached`守护进程,但是你可以自己安装:
```
 sudo /usr/local/packager/packager.py install tools-memcached

```


## 其它

#### 不支持32位系统

#### php.ini
`php.ini` 位于`/usr/local/php5/lib/php.ini`,其它的配置位于`/usr/local/php5/php.d/`。这个`php.ini`是依据`php.ini-development`。

#### Liip php.ini

Liip的php.ini 位于` /usr/local/php5/php.d/99-liip-developer.ini.`。

#### 重新安装到新目录
每次更新都会重新安装到一个新目录,你必须手动的去合并。


#### apc, twig and xslcache

apc, twig and xslcache 需要在 `/usr/local/php5/php.d/50-extension-$name.ini`文件中手动开启.

#### xdebug
`xdebug`需要在`/usr/local/php5/php.d/50-extension-xdebug.ini`文件中手动配置。

#### apache error with ulimit
如果你运行apache的时候遇到`/usr/sbin/apachectl: line 82: ulimit: open files: cannot modify limit: Invalid argument`这样的错误信息,请看[这里](http://blog.deversus.com/2010/11/mac-os-1065-apachectl-usrsbinapachectl-line-82-ulimit-open-files-cannot-modify-limit-invalid-argument/)


#### 历史版本以及所有
[请看这里](https://blog.liip.ch/archive/2011/04/13/php-5-3-for-os-x-10-6-one-line-installer.html)

## 手动安装(可选)
如果你不相信我们,你可以选择手动安装,运行如下代码:
```
curl -s -o /tmp/packager.tgz http://php-osx.liip.ch/packager/packager.tgz
sudo tar -C /usr/local  -xzf /tmp/packager.tgz
sudo /usr/local/packager/packager.py install 5.4-frontenddev
```
如果你已经安装了`packager`,只是想更新php包,你可以运行如下代码:
```
sudo /usr/local/packager/packager.py install tools-frontenddev

```


## 包含的扩展

bcmath bz2 calendar Core ctype curl date dom dtrace ereg exif fileinfo filter ftp gd gettext hash iconv imap intl json ldap libxml mbstring mcrypt memcache memcached mhash mongo mssql mysql mysqli mysqlnd OAuth odbc openssl pcntl pcre PDO pdo_dblib pdo_mysql pdo_pgsql pdo_sqlite pgsql Phar posix Reflection session shmop SimpleXML soap sockets solr SPL SQLite sqlite3 standard sysvmsg sysvsem sysvshm tidy tokenizer wddx xdebug xhprof xml xmlreader xmlrpc xmlwriter xsl zip zlib Xdebug

已安装但是没有开启:
available but disabled by default: apc, xslcache, twig, uploadprogress

可以通过[phpinfo](http://php-osx.liip.ch/info.html),了解更多。

## 卸载
修改`/etc/apache2/httpd.conf`

```
LoadModule php5_module /usr/local/php5/libphp5.so

```
为:
```
LoadModule php5_module        libexec/apache2/libphp5.so

```
同时删除位于`/etc/apache2/other`的`+php-osx.conf `和`+entropy-php.conf`文件以及删除` /usr/local/php5-*`全部文件。


## 重新安装
重新安装一个已近存在的包,必须要删除`/usr/local/packager/registry.log`中记录的版本信息。
```
sudo $EDITOR /usr/local/packager/registry.log

```
要重新删除全部包的记录信息可以删除这个文件:
```
sudo rm -rf /usr/local/packager/registry.log
```

## Latest releases
```
PHP 7.0.7 for OS X 10.10/10.11 uploaded at 2016-05-26

PHP 5.6.21 for OS X 10.10/10.11 uploaded at 2016-05-24
PHP 5.6.19 for OS X 10.8/10.9   uploaded at 2016-03-10

PHP 5.5.35 for OS X 10.10/10.11 uploaded at 2016-05-24
PHP 5.5.33 for OS X 10.8/10.9   uploaded at 2016-03-10
PHP 5.5.33 for OS X 10.6/10.7   uploaded at 2016-03-10

PHP 5.4.45 for OS X 10.10/10.11 uploaded at 2016-03-10
PHP 5.4.45 for OS X 10.8/10.9   uploaded at 2016-03-10
PHP 5.4.45 for OS X 10.6/10.7   uploaded at 2015-12-26

PHP 5.3.29 for OS X 10.10/10.11 uploaded at 2014-10-19
PHP 5.3.29 for OS X 10.8/10.9   uploaded at 2014-08-18
PHP 5.3.29 for OS X 10.6/10.7   uploaded at 2014-08-14
```


## 翻译自:
[http://php-osx.liip.ch/](http://php-osx.liip.ch/)