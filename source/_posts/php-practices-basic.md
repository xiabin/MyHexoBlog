title: php最佳实践-基础篇
date: 2017-03-04 10:49:45
categories: [PHP]
tags: [PHP 最佳实践]
---
"php是世界上最好的语言",它可以让各个层面的程序员都可以高效快速的完成任务。正是因为这些“高效快速”，导致很多phper忽略了php的基础。本文主要分享下php在基础编码的实践。 
主要讲述以下几点：
* 比较运算符(Comparison operators)
* 条件语句(Conditional statements)
* Switch语句 （Switch statements
* 全局命名空间
* 字符串(String)
* 字符串类型（String types）
* Nowdoc 语法（Nowdoc syntax）
* Heredoc 语法(Heredoc syntax)
* 三元运算符(Ternary operators)
* 变量声明(Variable declarations)

<!--more-->


## 比较运算符(Comparison operators)
在php中比较运算符经常是被忽略的一点，正是因为忽略了其细节点，导致了一些异常的问题。比如，我们常说的严格等于和非严格等于（`==`和 `===`),如下：
```
<?php
$a = 5;   // 5 是一个integer
var_dump($a == 5);   // 只比较值; return true
var_dump($a == '5');     // 只比较直（忽略了类型）; return true
var_dump($a === 5);      //比较了值和类型  (integer vs. integer); return true
var_dump($a === '5');    // 比较了值和类型 (integer vs. string); return false



//非严格等于
if (strpos('testing', 'test')) {    // 返回的是false,因为test是在位移为0的地方发现的，strpos返回0，0会被‘理解’为false（类型转换）
    // code...
}
//严格等于
if (strpos('testing', 'test') !== false) {    // 返回的是true (0 !== fasle)
    // code...
}

```
工作中只要注意就好，然后记不清楚的时候可以通过`类型对照表去参考下`，下面列举一下：  
### Comparisons of $x with PHP functions
![](http://7xokym.com1.z0.glb.clouddn.com/php-note71.png)
### Loose comparisons with ==
![](http://7xokym.com1.z0.glb.clouddn.com/php-note72.png)
### Strict comparisons with ===
![](http://7xokym.com1.z0.glb.clouddn.com/php-note73.png)

### 参考链接：
* [Comparison operators](http://php.net/language.operators.comparison)
* [Comparison table](http://php.net/types.comparisons)
* [Comparison cheatsheet](http://phpcheatsheets.com/index.php?page=compare)


### 条件语句(Conditional statements)
我们在使用条件语句的时候经常会有一种潜意识就是“使用if的时候后面肯定会接一个else”，其实在很多时候后面的else并不是必要的，尤其是在函数里面或者是类的方法里面。如下：
```
function test($a)
{
    if ($a) {
        return true;
    } else {
        return false;
    }
}
//可以写成
function test($a)
{
    if ($a) {
        return true;
    }
    return false;    // else不是必须的
}
//或者这样写,更简洁
function test($a)
{
    return (bool) $a;
}
```
## Switch语句 （Switch statements）
switch 语句是用来避免无止境的`if`和`esle if`,但是如下几个问题应该注意：
* switch 语句仅仅是做值得比较，不做类型的比较相当于`==`
* case会一直向下执行,全部执行完之后没有都没哟匹配到，则执行default（前提是你定义了default)
* 如果case 中没有`break`则会执行每个case,直到遇到`break`


## 全局命名空间
当我们在使用命名空间的时候，我们可能会遇到自己写的方法或者类和系统内置的方法同名，这个时候我们要调用系统内置方法或者类则需要用到`全局命名空间`。

```
namespace phpdevgroup;

function fopen()
{
    $file = \fopen();    // 我们的方法名称和内置的同名，要调用内置方法则通过全局命名空间在前面加 '\'.
}

function array()
{
    $iterator = new \ArrayIterator();    // ArrayIterator是内置的类.为了和自己的类区分可以在前面加上'\' 
}
```
### 参考链接：
* [Global space](http://php.net/language.namespaces.global)
* [Global rules](http://php.net/userlandnaming.rules) 


## 字符串(String)
### 连接（Concatenation）
* 假如你要定义的字符串超出了推荐的长度（推荐是120个字符）可以考虑使用拼接字符串
* 考虑可读性我们最好使用连接运算符而不是使用连接赋值运算符
* 在一个变量定义的字符串时，假如有字符串有换行我们应该将换行符单写一行
如下：
```
<?php
$a  = 'Multi-line example';    // 连接赋值运算符 (.=)
$a .= "\n";
$a .= 'of what not to do';

// vs

$a = 'Multi-line example'      // 连接运算符 (.)
    . "\n"                     // 换行符新起一行
    . 'of what to do';
```

### 参考链接
[String Operators](http://php.net/language.operators.string)


## 字符串类型（String type）
字符串就是有一些列字符组成，看起来好像很简单。其实不然，有几种不同类型的字符串他们有不同的语法，当然也有不同的功能。

### 单引号（Single quotes）
简单的说单引号不会对字符串里面的变量进行解析：
比如`'some $thing'`实际输出` some $thing`,假如是双引号，则会将`$thing`替换为变量的实际内容，倘若没有定义则会报错。
```
<?php
echo 'This is my string, look at how pretty it is.';    //不需要解析变量的简单字符串

/**
 * Output:
 *
 * This is my string, look at how pretty it is.
 */

```
#### 参考链接
* [Single quote](http://php.net/language.types.string#language.types.string.syntax.single)
* 
### 双引号（Double quotes）
双引号功能比较强大，不仅会解析上面提到的变量，而且会解析所有类型的特殊字符，例如\ n换行，\ t为制表符等。
```
<?php
echo 'phpdevgroup is ' . $adjective . '.'     
    . "\n"                                       // 一个单引号示例，它使用多个连接变量和转义字符串
    . 'I love learning' . $code . '!';

// vs

echo "phpdevgroup is $adjective.\n I love learning $code!"  // 使用双引号的写法
       
```

双引号可以包含变量;这被称为“插值”。
```
<?php
$juice = 'plum';
echo "I like $juice juice";    // Output: I like plum juice
```
当使用插值时，通常情况下会把变量的值插入进另一个字符中。这将导致一些混淆，比如到底哪个部分是字符串哪个部分是变量名。
我们可以通过将变量放在大括号里的方法解决这个问题：
```
<?php
$juice = 'plum';
echo "I drank some juice made of $juices";    // $juice 不能被解析

// vs

$juice = 'plum';
echo "I drank some juice made of {$juice}s";    // $juice 可以被解析

/**
 * 大括号里面的复杂变量也可以被解析
 */
 
$juice = array('apple', 'orange', 'plum');
echo "I drank some juice made of {$juice[1]}s";   // $juice[1] 被解析
```

### 参考链接
[Double quotes](http://php.net/language.types.string#language.types.string.syntax.double)


## Nowdoc 语法（Nowdoc syntax）
Nowdoc语法在5.3中引入，它和单引号的功能是一样的。区别在于对于多行的字符串它不需要拼接。
```
<?php
$str = <<<'EOD'             //  开头是<<<
Example of string
spanning multiple lines
using nowdoc syntax.
$a does not parse.
EOD;                        // 最后的 'EOD'必须放在新的一行而且是行的起始位置。

/**
 * Output:
 *
 * Example of string
 * spanning multiple lines
 * using nowdoc syntax.
 * $a does not parse.
 */
```
### 参考链接
* [Nowdoc syntax](http://php.net/language.types.string#language.types.string.syntax.nowdoc)

##  Heredoc 语法(Heredoc syntax)

它和双引号的功能是一样的。区别在于对于多行的字符串它不需要拼接。
```
<?php
$a = 'Variables';

$str = <<<EOD               // 开始 <<<
Example of string
spanning multiple lines
using heredoc syntax.
$a are parsed.
EOD;                        // 最后的 'EOD'必须放在新的一行而且是行的起始位置。

/**
 * Output:
 *
 * Example of string
 * spanning multiple lines
 * using heredoc syntax.
 * Variables are parsed.
 */
```

### 参考链接
* [Heredoc syntax
](http://php.net/language.types.string#language.types.string.syntax.heredoc)


*ps:Nowdoc syntax和Heredoc syntax语法上的区别:*
```
Nowdoc syntax
<<<`EOD`
EOD

Heredoc syntax
<<<EOD
EOD
```
*而且没有固定说非要EOD来放在开头和结尾*

## 谁更快？
经常会听到有人说单引号的字符串执行起来比双引号的快，其实不以为然。(后面有业内同行写的帖子可参考)


如果要定义单个字符串并且不做任何连接或者插值，那么单引号或双引号字符串执行速度完全相同。

如果要连接多个不同类型字符串，或将变量插入双引号字符串，则结果可能会有所不同。如果只是少数的不同字符串连接则，连接会快得多。倘若要连接很多字符串则插值的方法会快得多。

其实从另一种角度考虑这种细微的优化我们可以不考虑，因为不管字符串怎么写，对程序的整体影响都不大。去分区这种微笑的差异只是为了理解的更加深刻。

### 参考链接
* [对于单引号执行速度比双引号快的反驳](http://nikic.github.io/2012/01/09/Disproving-the-Single-Quotes-Performance-Myth.html)

## 三元运算符(Ternary operators)


三元运算符是一种精简代码的好方法，但不能过度使用，要保证可读性。
```
<?php
$a = 5;
echo ($a == 5) ? 'yay' : 'nay';
```
相比之下，这种为了减少行数而牺牲可读性的写法不可取
```
<?php
echo ($a) ? ($a == 5) ? 'yay' : 'nay' : ($b == 10) ? 'excessive' : ':(';    // 过多嵌套，可读性差
```


使用三元运算符'返回'值时应该使用正确的语法。
```
<?php
$a = 5;
echo ($a == 5) ? return true : return false;    // 会报错

// vs

$a = 5;
return ($a == 5) ? 'yay' : 'nope';    // 返回 'yay'
```

应该注意，你不需要使用三元运算符来返回布尔值。
```
<?php
$a = 3;
return ($a == 3) ? true : false; //假如 $a == 3 则返回true否则false
// vs

$a = 3;
return $a == 3; // 假如 $a == 3 则返回true否则false
```


## 在使用三元运算符的时候加上括号提高可读性，同时括号也可以避免逻辑运算的错误
```
<?php
$a = 3;
return ($a == 3) ? "yay" : "nope"; // 加上括号

// vs

$a = 3;
return $a == 3 ? "yay" : "nope"; // 不加括号

return ($a == 3 && $b == 4) && $c == 5;//逻辑运算可读性更好

```
php5.3之后，使用三元运算符可以这么写
```
expr1？：expr3”返回expr1，否则返回expr3。
```

php7中三元运算符，可以这么写 
```php
expr1??expr2 //等价于 expr1?expr1:expr2
```
### 参考链接
* [三元运算符](http://php.net/language.operators.comparison)


## 变量声明(Variable declarations)


有时，我们在开发的过程中试图用预定义的变量的方法使得代码更整洁。但是我们有可能在做两倍内存的消耗。下面的例子我们假设一个字符串的数据是1MB，通过复制变量，实际我们用了2MB的数据。

```
<?php
$about = 'A very long string of text';    // 用了 2MB 内存
echo $about;

// vs

echo 'A very long string of text';        // 用了 1MB 内存
```
