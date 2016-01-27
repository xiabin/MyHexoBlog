title: 让面向对象更加灵活 的模式
date: 2016-01-19 15:15:32
categories: Design pattern
tags: [Design pattern]
---
这里主要介绍如下模式：
- `组合(Compostite)模式`：将一组对象合为 可像单个对象一样被使用的结构
- `装饰(Decorator)模式`：通过在运行时合并对象来扩展功能的一种灵活机制
- `外观(Facade)模式`：为复杂或多变的系统创建一个简单的接口
<!-- more -->

### 组合(Compostite)模式
这里说下一个区别：
- 聚合：表示两个对象之间是整体和部分的弱关系，部分的生命周期可以超越整体。如电脑和鼠标。
- 组合：表示两个对象之间是整体和部分的强关系，部分的生命周期不能超越整体，或者说不能脱离整体而存在。组合关系的“部分”，是不能在整体之间进行共享的。
```java
abstract class Unit{
    abstract function doSomething();
}

class AUnit extends Unit
{
    function doSomething()
    {
        // TODO: Implement doSomething() method.
    }
}

class BUnit extends Unit
{
    function doSomething()
    {
        // TODO: Implement doSomething() method.
    }
}

class Units
{
    private $unitsArray = array();

    public  function addUnit(Unit $unit){
        array_push($this->unitsArray, $unit);
    }
}
```
我们可以通过组合模式去实现用对待单一对象的形式去对应一组对象。组合模式比较适合简单的结构，但是随便业务的复杂，代码也越来越难维护。



### 装饰(Decorator)模式
在web项目中我们经常会进行请求进行过滤，那么我们可以使用装饰模式。
```java
class RequestHelper{

}

abstract  class ProcessRequest{
    abstract function process(RequestHelper $req);
}

class MainProcess extends ProcessRequest{
    public function process(RequestHelper $req)
    {
        // TODO: Implement process() method.
    }
}

abstract  class DecorateProcess extends ProcessRequest{
    protected $processrequest;
    function __construct(ProcessRequest $pr)
    {
        $this->processrequest = $pr;
    }
}

class LogRequest extends DecorateProcess{
    public function process(RequestHelper $req)
    {
        // TODO: Implement process() method.
    }
}

class AuthenticateRequst extends DecorateProcess{
    public function process(RequestHelper $req)
    {
        // TODO: Implement process() method.
    }
}

class StructureRequest extends DecorateProcess{
    public function process(RequestHelper $req)
    {
        // TODO: Implement process() method.
    }
}


$process = new AuthenticateRequst(new StructureRequest(new LogRequest(new MainProcess())));

$process->process(new RequestHelper());
```
其实这就是一个拦截过滤器的例子。
装饰对象就是对子对象的包装，所以当基类中方法比较多的时候就会显得比较复杂。我们在开发中经常会用到__call()方法来实现类似的操作。

### 外观(Facade)模式

外观模式就是为项目中复杂的部分创造一个简单，清晰的对外接口。了，内部实现对使用者是透明的。
比如在某个应用初始化的时候实际上要初始化很多子应用。
```java
class ChildAppA{
    public function init(){
        // TODO:
    }
}
class ChildAppB{
    public function init(){
        // TODO:
    }
}


class App{

    private $childAppA;
    private $childAppB;
    public function  __construct()
    {
        $this->childAppA =  new ChildAppA();
        $this->childAppB =  new ChildAppB();
    }
    
    public function  init(){
        $this->childAppA->init();
        $this->childAppB->init();
    }
}
```
外观模式是一个十分简单的概念。是为一个分层的系统或者为一个子系统创建一个单一的入口。

### 总结
我们在这里讨论的是几种类和对象的组织方式，通过这几种方式我们可以在面向对象编程的基础上面更加灵活的使用类和对象。