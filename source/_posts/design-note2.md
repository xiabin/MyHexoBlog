title: 用于生成对象的模式
date: 2016-01-15 09:42:23
categories: Design pattern
tags: [Design pattern]
---
这里主要介绍了我们在工作中会用到的几种创建对象的模式：
- 单例(Singleton)模式：生成一个且只生成一个对象实例的特殊类。
- 工厂方法(Factory Method)模式：构建创建者类的继承层级。
- 抽象工厂(Abstract Factory)模式：功能相关产品(产品族)的创建。
- 原型(Prototype)模式：使用克隆来生成对象。
<!--more-->

### 单例(Singleton)模式
我们在开发过程中可能会经常遇到要使用全局变量，而对全局变量的把控不稳定可能会是引发bug的主要原因之一。我们在web开发的时候经常会遇到很多公用的配置信息，比如数据库的DSN，url根目录u，文件路径等数据。我们可以用一个对象来存储它们，并运用单例模式。既可以保证在系统中可以随时被使用，又可以保证不与一些全局变量冲突。而且 整个系统中只有一个这样的变量。
```java
class Preferences{
	private $props = array();

	private static $instance;

	private function __construct(){}

	public function setProperty($key, $value){
		$this->props[$key] = $value;
	}

	public function getProperty($key){
		return $this->props[$key];
	}

	public static function getInstance()
	{
		if(empty(self::$instance)){
			self::$instance = new Preferences();
		}
		return self::$instance;
	}
}

$pref = Preferences::getInstance();
$pref->setProperty('a','123');
print_r($pref->getProperty('a'));
```
在面向对象的开发环境中，单例模式是对全局变量的一种改进。

### 工厂方法(Factory Method)模式
我们把简单工厂模式和工厂方法模式放在一起来说
首先说简单工厂模式：
简单工厂模式是通过创建静态方法类创建对象
```php 
interface People
{
    public function say();
}

class Chinese implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "Chinese say";
    }
}

class American implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "American say";
    }

}

class PeopleFactoty
{
    static function createChinses()
    {
        return new Chinese();
    }

    static function createAmerican()
    {
        return new  American();
    }

}
$people = PeopleFactoty::createChinses();
$people->say();
$people = PeopleFactoty::createAmerican();
$people->say();
```

工厂方法模式：
工厂方法模式是定义一个创建的接口，让子类去决定实例化哪个对象。
```java
interface People
{
    public function say();
}

class Chinese implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "Chinese say";
    }
}

class American implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "American say";
    }

}

interface CreatePeople{
    public  function create();
}

class CreateChinese implements CreatePeople{

    public function __construct()
    {
    }

    public function create()
    {
        return new Chinese();
    }
}


class CreateAmerican implements CreatePeople{

    public function __construct()
    {
    }

    public function create()
    {
        return new Chinese();
    }
}
$createPeople  =  new CreateAmerican();
$people = $createPeople->create();
$people->say();

$createPeople  =  new CreateChinese();
$people = $createPeople->create();
$people->say();
```

### 抽象工厂(Abstract Factory)模式
在一个大型额应用里面，我们用抽象工厂模式可以生产一组相关的类。
```java
interface People
{
    public function say();
}


class ChineseMan implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "ChineseMan say";
    }
}

class ChineseWomen implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "ChineseWomen say";
    }
}

class AmericanMan implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "AmericanMan say";
    }

}


class AmericanWomen implements People
{
    public function __construct()
    {
    }

    public function say()
    {
        return "AmericanWomen say";
    }

}

interface CreatePeople{
    public  function createMan();
    public  function createWomen();
}

class CreateChinese implements CreatePeople{

    public function __construct()
    {
    }

    public  function createMan(){
        return new ChineseMan();
    }
    public  function createWomen(){
        return new ChineseWomen();
    }
}


class CreateAmerican implements CreatePeople{

    public function __construct()
    {
    }

    public  function createMan(){
        return new AmericanMan();
    }
    public  function createWomen(){
        return new AmericanWomen();
    }
}
$createPeople  =  new CreateAmerican();
$man = $createPeople->createMan();
$man->say();
$women = $createPeople->createWomen();
$women->say();

$createPeople  =  new CreateChinese();
$man = $createPeople->createMan();
$man->say();
$women = $createPeople->createWomen();
$women->say();
```

对于三种工厂模式的总结：
- 区别：
`简单工厂模式`:用来生产同一等级结构中的任意产品。对与增加新的产品，无能为力
`工厂模式`：用来生产同一等级结构中的固定产品。（支持增加任意产品）   
`抽象工厂`：用来生产不同产品族的全部产品。（对于增加新的产品，无能为力；支持增加产品族）  
以上三种工厂方法在等级结构和产品族这两个方向上的支持程度不同。所以要根据情况考虑应该使用哪种方法
- 适用范围：
`简单工厂模式`：
工厂类负责创建的对象较少，客户只知道传入工厂类的参数，对于如何创建对象不关心。
`工厂方法模式`：
当一个类不知道它所必须创建对象的类或一个类希望由子类来指定它所创建的对象时，当类将创建对象的职责委托给多个帮助子类中得某一个，并且你希望将哪一个帮助子类是代理者这一信息局部化的时候，可以使用工厂方法模式。
`抽象工厂模式`：
一个系统不应当依赖于产品类实例如何被创建，组合和表达的细节，这对于所有形态的工厂模式都是重要的。这个系统有多于一个的产品族，而系统只消费其中某一产品族。同属于同一个产品族的产品是在一起使用的，这一约束必须在系统的设计中体现出来。系统提供一个产品类的库，所有的产品以同样的接口出现，从而使客户端不依赖于实现。
无论是简单工厂模式、工厂模式还是抽象工厂模式，它们本质上都是将不变的部分提取出来，将可变的部分留作接口，以到最大程度上的复用。究竟用哪种设计模式更适合，这要根据具体的业务需求来决定。


### 原型(Prototype)模式
 原型模式通过复制已经存在的实例来返回新的实例，而不是新建实例，并且原型（被复制的实例）是可定制的；原型模式多用于创建复杂的或耗时的实例，这种情况下，复制一个已经存在的实例是程序运行更高效无疑是一种好办法
```java
 class Fish{};
 class Sea{}
 class EarthSea extends Sea{
     private $fish;
     public function __construct(Fish $fish)
     {
         self::__construct();
         $this->fish = $fish;
     }
 
     //deep copy
     public function __clone()
     {
         $this->fish = clone  $this->fish;
     }
 }
 class MarSea extends Sea{}
 
 class Forest{}
 class EarthForest extends Forest{}
 class MarForest extends Forest{}
 
 class Prototype{
     private $sea;
     private $forest;
     public  function __construct(Sea $sea,Forest $forest)
     {
         $this->sea = $sea;
         $this->forest = $forest;
     }
 
     public function getSea(){
         return clone $this->sea;
     }
 
     public function getForest(){
         return clone $this->forest;
     }
 }
 ```
 
### 总结
在这里我们主要讨论用于生成对象的窍门，讨论了支持全局访问的单例，以及应用多态原型生产对象的工厂方式模式。顺带延伸的说明了三种工厂模式的特性。最后，简单的说了下原型模式，学习使用对象克隆的方式来生成对象。 