title: Codeigniter集成doctrine2
date: 2016-01-27 16:01:12
categories: PHP
tags: [PHP]
---
Doctrine是一个ORM（Object-relational mapper），提供php数据库和PHP对象的映射。他和其他的ORM一样都是为了保证持久层和逻辑层的分类而存在的。这里主要介绍怎么在CI里面集成Doctrine。
<!--more-->


首先我们安装CI的框架这里使用的是Codeigniter3+,框架的下载就不多说了。
接下来我们的步骤大概如下：
- 下载Doctrine
- 集成进CI

下面我们一步一步的操作：
- 在`application/third_party`下面新建文件夹``
- 在`Codeigniter`文件夹里面新建`composer.json`文件，文件里面内容如下:
```java
{
    "require": {
        "doctrine/orm": "~2.5.4"
    }
}
```
然后执行`composer insall`,不会使用的composer的请自行学习。
 - 接着在`application/libraries`文件夹下面新建`Doctrine.php`文件,文件内容如下:
 ```java
 use Doctrine\Common\ClassLoader,
     Doctrine\Common\Annotations\Annotation,
     Doctrine\ORM\Configuration,
     Doctrine\ORM\EntityManager;
 
 class Doctrine
 {
     public $em;
 
     public function __construct()
     {
         //加载数据库配置
         require_once APPPATH . 'config/database.php';
         //引入Doctrine的autoload文件
         include_once APPPATH . 'third_party/Doctrine/vendor/autoload.php';
 
         // 自动加载实体对象，第一个参数书命名空间，第二个参数是文件路径这个例子就是在`models/Entity`下面
         $entityClassLoader = new ClassLoader('Entity', APPPATH . 'models');
         $entityClassLoader->register();
         // 自动加载实体的代理对象，对我位置`models/Proxies`
         $proxiesClassLoader = new ClassLoader('Proxies', APPPATH . 'models');
         $proxiesClassLoader->register();
 
         // 生成Configuration对象
         $config = new Configuration;
 
         // 设置缓存
         if (ENVIRONMENT == 'development')  // set environment in index.php
             // 开发环境下面用数组的方式缓存，便于调试
             $cacheDriver = new \Doctrine\Common\Cache\ArrayCache;
         else {
             // 生产环境下面用redis，当然你也可以其它的缓存产品
             $redis = new Redis();
             $redis->connect('redis_host', 'redis_port');
             $cacheDriver = new \Doctrine\Common\Cache\RedisCache();
             $cacheDriver->setRedis($redis);
         }
         $config->setMetadataCacheImpl($cacheDriver);
         $config->setQueryCacheImpl($cacheDriver);
 
         $driverImpl = $config->newDefaultAnnotationDriver(APPPATH . 'models/Entity');
         $config->setMetadataDriverImpl($driverImpl);
 
         // 设置代理对象的路径
         $config->setProxyDir(APPPATH . '/models/Proxies');
         $config->setProxyNamespace('Proxies');
 
         // 是否输出日志
         if (ENVIRONMENT == 'development') {
             $logger = new \Doctrine\DBAL\Logging\EchoSQLLogger();
             $config->setSQLLogger($logger);
         }
 
 
         $config->setAutoGenerateProxyClasses(true); // 设置自动生成代理对象，只在开发模式下使用
 
         //获取CI的数据库配置
         $connectionOptions = array(
             'driver' => 'pdo_mysql',
             'user' => $db['default']['username'],
             'password' => $db['default']['password'],
             'host' => $db['default']['hostname'],
             'dbname' => $db['default']['database'],
             'charset' => $db['default']['char_set'],
             'driverOptions' => array(
                 'charset' => $db['default']['char_set'],
             ),
         );
         生成EntityManager对象，整个CI框架中我们主要都依赖它
         $this->em = EntityManager::create($connectionOptions, $config);
     }
 }
 ```
 
 上面说的是高级的配置，用到缓存以及代理对象。
 还有种低级配置的这里不详细描述下面只贴出主要代码
 ```java
   $connection_options = array(
         'driver'        => 'pdo_mysql',
         'user'          => $db['default']['username'],
         'password'      => $db['default']['password'],
         'host'          => $db['default']['hostname'],
         'dbname'        => $db['default']['database'],
         'charset'       => $db['default']['char_set'],
         'driverOptions' => array(
             'charset'   => $db['default']['char_set'],
         ),
     );

     // With this configuration, your model files need to be in application/models/Entity
     // e.g. Creating a new Entity\User loads the class from application/models/Entity/User.php
     $models_namespace = 'Entity';
     $models_path = APPPATH . 'models';
     $proxies_dir = APPPATH . 'models/Proxies';
     $metadata_paths = array(APPPATH . 'models/Entity');

     // Set $dev_mode to TRUE to disable caching while you develop
     $dev_mode = true;

     // If you want to use a different metadata driver, change createAnnotationMetadataConfiguration
     // to createXMLMetadataConfiguration or createYAMLMetadataConfiguration.
     $config = Setup::createAnnotationMetadataConfiguration($metadata_paths, $dev_mode, $proxies_dir);
     $this->em = EntityManager::create($connection_options, $config);

     $loader = new ClassLoader($models_namespace, $models_path);
     $loader->register();
 ```
这样基本就把Doctrin集成进去了。

调用的时候而已参考如下代码：
```java
$this->load->library('doctrine');
$em = $this->doctrine->em;

$userGroup =$em->getRepository('Entity\UserGroup')->find(1);
if ($userGroup === null) {
    echo "No userGroup found.\n";
    exit(1);
}
echo "---------------<br/>";
foreach($userGroup->getUsers() as $value){
    echo $value->getUsername()."<br/>";
}
```

具体的demo可以访问[https://github.com/xiabin/CodeIgniter-Doctrine](https://github.com/xiabin/CodeIgniter-Doctrine)

参考文献：
- [http://www.doctrine-project.org/](http://www.doctrine-project.org/)
- [CodeIgniter 3.0整合Doctrine2 ](http://lattecake.com/post/20042)
