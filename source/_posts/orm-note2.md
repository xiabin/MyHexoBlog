title: 实战中Active Record和Data Mapper的选择
date: 2016-02-01 15:44:19
categories: Design pattern
tags: [ORM,PHP]
---
许多被广大程序员们推荐的书，往往都是一些有着很多年经验的程序员，而且他们还有一个共同点——都是强类型面向对象编程语言的使用者，比如说Java和Smalltalk。这些语言发展的年代没有很多像现在我们使用的成熟框架。实际上，正式这些书的作者，也开发了许多现在程序员都喜欢使用的各种工具。
 近几年新出现一些语言，而伴随这些语言的出现也有许多框架诞生，但是很多都是借鉴历史比较老的语言的框架思想，而有些框架相对来说比较复杂。从某种角度来说他给我们提供了很多便捷的工具，以至于开发人员都很喜欢使用它，从而易导致了大家对某种框架实现思想的深入，尤其是在国内，很多程序员都是停留在会用的层面上。我觉得这也是国内软件工程发展影响因素之一吧。
 其实更多是因为市场的需求吧，因为市场需要快速的开发，需要快速的诞生产物。通过这些工具可以解决这种需求，但是这让我们的程序员们产生一种“关注功能，而不关注实现”的思想。
 其实仔细想想我们在使用框架的过程中，失去了很多在没有框架年代那些开发者们学到的东西。我们变得十分依赖工具，仔细回想你可能会觉得我们已经让框架变成了产品，而不是说通过框架来开发产品了。举个很简单的例子，开发中我们经常会强调“简单的controller层，复杂的model层”,但是很多时候开发过程中并不是这样的，我们可能会把很多业务逻辑写在controller里面去。
 我们在学习软件架构的时候，可能都会遇到这样的一个问题，应该选择什么模式的ORM？这个问题可能很多人都讨论过，我在这里主要通过一个列子去说明我们在实战中，我们如何在Active Record和Data Mapper模式的ORM中做出选择。我么会介绍这两种模式实现的优缺点。
  <!--more-->
 
#### 首先我们假设一个案例
 **一个简单的职员和票的关系**
 - 每一张票都有类型
 - 在某种场合下这个票可以分给一个职员，这种场合可能是：
    - 一个职员可以负责很多种类的票
    - 这个客户只呗分配一张在他负责范围内的票
 - 每张票都有状态，假如这个状态是关闭，那么当它收到信息的时候状态就会打开
 
#### 两种实现方式

##### Active Record
使用这种模式我们不需要关注实体的属性是值，因为它是和数据库的结构有关的，我们可以通过表结构来生成。在一些优秀的ORM框架中，都自带生成工具。

示例代码：

票
```java
<?php namespace Help;

use Some\Package\SomeOrm;

class Ticket extends SomeOrm {

    $table = 'tickets';

    $hasMany = 'Message';

    $hasOne = array('Staffer', 'Category');

}
```
 信息
```java
<?php namespace Help;

use Some\Package\SomeOrm;

class Message extends SomeOrm {

    $table = 'messages';

}
```
 职员 
 ```java
 <?php namespace Help;
 
 use Some\Package\SomeOrm;
 
 class Staffer extends SomeOrm {
 
     $table = 'staffers';
 
     $hasMany = 'Category'
 
 }
 ```
 
 （这里我们忽略了分类，因为这个类里面就是一个字符串属性。）
 上面我们定义了我们的models，下面的代码展示我们怎么使用：
 首先为票分配类别：
 ```java
 $ticket = new Ticket;
 $ticket->subject = 'Some Subject';
 $ticket->dateOpened = new \DateTime;
 
 // A facade, or whatever :P
 $category = Category::find('name', 'Default');
 
 $ticket->category = $category->id;
 
 $ticketId = $ticket->save();
 ```
 然后我们给票分配一个职员：
 ```java
 $ticket = Ticket::find($ticketId);
 
 $staffer = Staffer::find($stafferId)
 
 // Assign staffer, but only if staffer has the
 // Ticket's category available
 foreach( $staffer->categories as $category )
 {
     if( $ticket->category == $category )
     {
         $ticket->staffer = $staffer;
     }
 }
 
 if( is_null($ticket->staffer) )
 {
     // Validation error handling
     // Staffer not assigned
 }
 ```
 上面我们定义一些models，我们还实现了一些业务逻辑。下面我们提出一个问题。
 ##### 逻辑代码写在什么地方？
 从上面可以看出我们逻辑代码并不是直接写在models层。那我们应该把逻辑代码放在什么地方了,比如说放在controller里面。但是这样做将会很难维护，我们必然会出现很多同样的代码。导致修改的时候会在多个地方修改。而且也违背了单一职责原则，因为controller层只是做请求的响应，具体的逻辑处理是不能放在这里面的。当然我们也可以在应用层以及model层之间添加一层logic层专门做业务的处理以及持久化的命令执行。
 也有人说我们可以定义一个存储器，这个对象主要用来管理某一个model的对象（处理持久化的请求），并处理义务逻辑。但是由于Active Record实体的特性我们会发现存储器管理的方式也违背了关注点分离，以及单一职责原则。主要因为这里面混杂了逻辑的代码，以及持久化的代码，还是耦合了在一起。因为这个存储器的主要目的其实是做持久化操作的。
 上述的这种操作可能会导致代码的换乱，比如说直接操作了实体里面的一个属性，然后在实例化，这就显得很混乱。因为从架构的角度来说写的操作应该是严格控制起来的。虽然有些有经验的开发者可以避免犯这样的错误，但是还是违背我们之前定义实体的约束，而直接修改对象的属性。其实很多人可能会问只是修改一个属性会有多大的影响，其实道理很简单因为这样的方式直接暴露了数据库里面的字段，不熟悉字段的人肯定不知道哪些是可以操作的哪些是不可以操作的，这样就有风险。
 然后在Data Mapper模式里面我们就解决上面的问题。
##### Data Mapper
在这种模式里面的存储器直接负责持久化，而且在实体中编写逻辑代码。但是这里面的实体与上述的实体不是一个概览，实体也可以被称之为领域模式，这里面的领域模型是业务的领域模型，是和业务结合在一起的对象，是业务的映射。换句话就是“领域驱动设计”的思想。上面的描述是一种贫血的领域模式，而我们接着要是的是充血的领域模式，简单的表单就是业务放在领域模型里面。

在一些Data Mapper成熟的ORM框架里面我们可以通过对应的实体生成数据库。

我们可以看如下代码：
上面一种方式的写法：
```java
$ticket = Ticket::find($id);
// Or, if we have a repository:
$ticket = $ticketRepo->findById($id);

// Some Operations on the Active Record model

$ticket->save();
```
而在这种模式里面：
```java
$ticket = $ticketRepo->find($id);

// Some operations on the Entity

$ticketRepo->save($ticket);
```

我们可以发现在第一段代码里面我们没有用存储器去做实例化。而在第二段代码里面我们通过存储器去统一处理了所有实体的实例化操作。
接着看下票的类：
```java
<?php namespace Help;

class Ticket {

    /**
     * Subject
     * @var string
     */
    protected $subject;

    /**
     * Messages in Ticket
     * Collection of Messages
     * @var Array
     */
    protected $messages = array();

    /**
     * Ticket Status
     * @var string
     */
    protected $status;

    /**
     * Staffer assigned to Ticket
     * @var Staffer
     */
    protected $staffer;

    /**
     * Category Assigned to Ticket
     * @var String
     */
    protected $category;

    /**
     * Create a new Ticket
     * @param string   $subject
     * @param DateTime $dateOpened
     * @param Customer $customer
     * @param Array    $messages
     */
    public function __construct($subject, $status="open", $category, $messages=array())
    {
        $this->subject = $subject;
        $this->updateStatus($status); // Use your setters!
        $this->category = $category;

        foreach( $messages as $message )
        {
            $this->addMessage($message);
        }
    }

    /**
     * Update the Ticket Status
     * @param  string $status
     * @throws \InvalidArgumentException
     * @return void
     */
    public function updateStatus($status)
    {
        $status = mb_strtolower($status);

        if( $status !== 'open' || $status !== 'closed' )
        {
            throw new \InvalidArgumentException('Illegal status given: '.$status);
        }

        $this->status = $status;
    }

    /**
     * Add a Message to the Ticket
     * @param Message $message
     */
    public function addMessage(Message $message)
    {
        // Re-open on a new Message
        // if Ticket is closed
        if( $this->status === 'closed' )
        {
            $this->status = 'open';
        }

        $this->messages[] = $message;
    }

    /**
     * Assign Ticket to Staffer
     * @param  Staffer $staffer
     * @throws \DomainException
     * @return void
     */
    public function assignStaffer(Staffer $staffer)
    {
        if( ! $staffer->isAvailableFor($this->category) )
        {
            throw new \DomainException('Staffer cannot be assigned to a Ticket of category '.$this->category);
        }

        $this->staffer = $staffer;
    }

    /**
     * Get Messages
     * @return Array
     */
    public function getMessages()
    {
        return $this->messages;
    }
}
```
这个时候你会发现这个领域模式是多么的充血。

下面是信息和职员的类：
```java
<?php namespace Help;

class Message {

    /**
     * Date Message Received
     * @var \DateTime
     */
    protected $dateReceived;

    /**
     * Message
     * @var String
     */
    protected $message;

    /**
     * Create a new Message
     * @param DateTime $dateReceived
     * @param String   $message
     */
    public function __construct(\DateTime $dateReceived, $message)
    {
        $this->dateReceived = $dateReceived;
        $this->message = $message;
    }
}

# File Staffer.php
<?php namespace Help;

class Staffer {

    /**
     * Staffer email
     * @var string
     */
    protected $email;

    /**
     * Categories Staffer
     *  is assigned To
     * @var Array
     */
    protected $availableCategories;

    public function __construct($email, Array $availableCategories)
    {
        $this->email = $email;
        $this->availableCategories = $availableCategories;
    }

    /**
     * Test if Staffer has the given Category
     * available to them.
     *
     * @param  String
     * @return Boolean
     */
    public function isAvailableFor($category)
    {
        return in_array($category, $this->availableCategories);
    }
}
```


在这里我们可以看出代码十分清晰，结构也很明确，而且也变得不耦合了。我们可以把逻辑都放在这里，这样每个层就专职的处理各自的事情了。
而且这个实体是一个对象，在单元测试的实现过程中我们也可以很好的处理，因为他们只依赖与他们自己。不需要依赖 与别的对象。
当我们要为某个员工分配票的时候这个时候可能就不可以像下面代码这样执行了：
```java
$staffer->assignedToTicket($ticket);
```
而是通过调用调用票的方法去实现。因为必须去通过票实体去修改职员以及信息的属性。这样的约束关系在Active Record里面是很难得到保障的。

#### 总结
这里我们不是说要排斥Active Record模式，我们只要通过一个案例去说明两种模式的各自特性。Active Record也在许多知名的框架中使用比如Rails, DJango, Laravel, SailsJS等等。因为相对来说可以更快的上手。
其实在实战中，你要根据你所开发的应用的类型的去判断你该使用什么样的模式。
如果你是一名php开发者，而且你要使用Data Mapper模式我推荐你使用[Doctrine2](http://www.doctrine-project.org/projects/orm.html).
想更深入的了解这两种模式的区分可以移步[DataMapper vs Active Record](/2016/01/28/orm-note1/).


#### 参考文献
[How We Code: ORMs and Anemic Domain Models](http://fideloper.com/how-we-code)
