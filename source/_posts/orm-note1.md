title: DataMapper vs Active Record
date: 2016-01-28 15:49:31
categories: Design pattern
tags: [ORM]
---
当我在开发过程中涉及到与数据库操作的时候，我们可能会考虑到需要用到一些ORM框架。可以认为ORM是介于应用程序和数据之间的媒介。通过ORM我们可以减少对于数据库增删改查的工作量,以及避免一些复杂的操作。
我想在大多数的工作中我们可能都不需要为使用什么类型的框架而烦恼。大多数的框架都支持ORM而且是以模块的模式使用。比如 Laravel 里面用的是[Eloquent](https://laravel.com/docs/5.2/eloquent).
然而在我们深入的去研究软件设计以及一些业务特定细节。我们会发现在不同类型的ORM进行研究是有价值的。我们目前常用的两种ORM是DataMapper和Active Record。
在这里，我们主要就是对这两中ORM的模式，各自的优缺点，以及我们该如何的选取进行讨论。
<!--more-->

#### ORM是什么，你为什么需要它？
在我们解释这两张ORM模式的区别之前，我们先说下你为什么需要ORM。
ORM(Object Relational Mapper)是介于数据库和 应用之间的。在OOP(Object Oriented Programming)中，我们通常把Object作为我们的关注点。
举个例子，如下：
```java
$user = new User;
$user->name = 'philipbrown';
```
然而在数据库中，(这里主要使用mysql)存储的字段如下：
```java
+----+-------------+
| id | username    |
+----+-------------+
| 1  | xiabin      |
+----+-------------+
```
一个User可能发布了许多文章，我们可以通过如下的形式获取文章：
```java
$posts = $user->posts;
```
在数据库中我们可能是这样存储的：
```java
+----+---------+-------+
| id | user_id | title |
+----+---------+-------+
| 1  | 2       | "..." |
+----+---------+-------+
```
当我们在面向对象的编程中，我们会用一个存储了所有相关属性和关系的对象单一对象。而数据库中存储是各个表之间相互独立的。
ORM可以让我们操作对象就像在操作数据库一样，相反也是这样的。
比如上面的例子我们的对象可能如下：
```java
class User{
    private $id;
    private $username;
    private $post;
}
```
然后里面的数据存在相互独立的表中，而我们借用ORM我们就可以对透过对象属性的操作去改变数据库的内容。

上面我解释了什么是ORM以及我们为什么要使用它，接下来我们说一下我们常见的两种ORM的模式。

#### Active Record模式的ORM

如果你使用过Laravel之类的框架，那么你可能对Active Record模式的ORM相对来说比较熟悉了。
一个典型的Active Record使用的例子如下：
```java
$user = new User;
$user->username = 'xiabin';
$user->save();
```

在这个例子中，我们创建一个User对象然后设置他们username熟悉为xiabin,并调用save方法，保存这个对象的值到数据库中。
Active Record模式的ORM映射一个对象对应数据库库中对应表的一行数据。比如上面的例子，User对象对应数据库中的User表中的一行数据。
在编写代码的时候我们不需要编写这个model对象的属性以及与数据库相关的方法，因为这个对象文件可以借用工具通过数据库的结构(schema of the database)自动生成。  
Active Record模式的一个优点就是我们可以简单调用对象的save方法，去更新数据库的值。每一个model对象都是继承BASE Active Record Object，而且你可以调用所有与持久化有关的方法。由于Active Record模式的直观，我们可以快速的上手。

#### Data Mapper模式的ORM
Data Mapper模式和Active Record模式的最大区别就是，Data Mapper模式的ORM完全把领域模式从持久化层中分离出来了。
下面是使用Data Mapper的例子：
```java
$user = new User;
$user->username = 'xiabin';
```
从上面我们并没有看到与Active Record模式不同之处。
然后Data Mapper模式ORM的Object仅仅是一个PHP的对象，我们需要关注数据库。但是你可能会想到一个问题，就是我们如何进行持久化操作了？因为上面的代码中我们并没有发现类似save的方法调用。正因为如此，与Active Record不同的是，在Data Mapper中我们需要一个实体管理器(Entity Manager);
参考如下代码：
```java
$user = new User;
$user->username = 'xiabin';
EntityManager::persist($user);
```
最大的优点就是我们可以只关注领域对象不需要关注数据库。这也意味着，我们的对象代码更简洁，因为我们不需要继承一些持久化有关的方法。同时我们就可以更少的关注持久化操作，可以更统一的管理持久化操作，因为我们不需要在代码中随意的调用类似save的方法。

#### 区别
在这里我们对两者之间的区别做一个简单的总结，讨论下每种模式的优缺点。
我们在无法去说这两种模式哪一种更好，只能说哪一种更适合，在实战中，结合我们对业务以及项目的规模我们去选择使用 一种合适的ORM。
那么到底哪些因素决定了你们的选择了，那么我认为有如下两种：
- ##### 应用程序的设计类型
首先我们可能会关注应用程序的设计类型的定义，就Web应用而言我们可能会有两种模式，一种是基于CURD的应用，一种是基于领域模式的应用。
基于CURD的应用我们可以清晰看到model对象合数据之间的映射。我们可能需要对实体进行增删改查。我们也会遇到要处理有关联的多个model的情况，但是绝大多数，并没有严格的去定义去如何执行这些情况的操作，而是交由用户去管理。
当你根据数据库的结构去进行应用程序设计的时候，我觉得Active Record是首选，你可以快速的在应用程序中使用。

反之，如果你们的应用程序设计的时候不依赖与数据库结构，而是根据业务规则那么就应该使用Data Mapper模式。它可以很好的控制持久化相关的操作，而且可以把业务规则封装在实体中。其实你会发现它体现领域驱动设计的模型。
- ##### 应用程序的规模
我觉得应用程序的规模也是决定你使用什么模式一个因素之一。如果你构建一个最小化可行产品(Minimum Viable Product, MVP)产品然后测试和收集用户的反馈，我觉得我们应该使用Active Record模式。因为一开始我们无法肯定业务的大多数规则，比如哪些业务重要，哪些可以固定在我们的应用程序中等等。

从另一种角度去说，加入我们我们要从一个存在的业务逻辑中去创建一个新的应用程序，那么我们可以采用Data Mapper模式的ORM。因为这个时候从我们业务的模式来说已经相对稳定了。如果使用Active Record模式我们可以需要强烈去改变我们的业务让他更适合我们的数据库的表结构，很显然这是不可取的。通过Data Mapper模式我们可以通过领域模型去封装业务规则，让在应用程序中更清晰更直观。

### 总结
我相信在实战中我们肯定会用到这两种模式的ORM，我们不能说谁最好，只能说谁最合适。由于选择了一种模式而导致应该程序出现问题，我们不能觉得是模式的问题，而是你选取的问题。通过上面的介绍我们可以在项目中，根据实际的业务去选择的更适合的ORM工具去解决我们的实际问题。
这里我们说的更多是偏向理论，关于实战的案例可以移步[实战中Active Record和Data Mapper的选择](/2016/02/01/orm-note2/)。

### 参考文献
[What’s the difference between Active Record and Data Mapper?](http://culttt.com/2014/06/18/whats-difference-active-record-data-mapper/)
