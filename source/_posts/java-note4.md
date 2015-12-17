title: Java基础加强
date: 2015-07-23 19:23:30
categories: Java #文章文类
tags: [Java]
---

1. JDK7.0介绍
新特性

2. 静态导入(了解)
作用：导入类中的静态成员
语法：``import static java.lang.System.out;``
或者``import static java.lang.System.*;``
    <!--more-->
3. 自动装箱和拆箱
基本类型---->包装类型：装箱
包装类型---->基本类型：拆箱
高速缓存：true、false、byte、介于~128-->+127间的整数 、介于\u0000到\u007F间的字符
因此这些基本类型的包装类对象，是同一个对象。

4. 增强for循环：
    设计的本意：抛弃迭代器
    增强for循环只能遍历数组和实现了Iteratable接口的对象。
    语法：
    ```java
    for(元素的类型 变量名:数组或实现了Iteratable接口的对象){
        System.out.println(变量名);
    }
    ```
    增强for循环的特点：只适合取数据。要想在遍历时改元素的值，请使用传统for循环。

5. 可变参数
可变参数就当做是数组即可。
一个方法的参数列表中，只能有一个可变参数，且必须放在最后。

6. 枚举
枚举可以定义构造方法
枚举可以定义抽象方法
枚举值放到第一行

7. 反射
反射乃框架设计之灵魂。
必须先得到类的字节码对象：Class clazz = Class.forName(类的完整类名);
如何得到类的字节码：
		Class.forName(类的完整类名);
		对象引用名称.getClass();
		类名.class
在反射方法时，如果方法的参数是一个数组，考虑到向下兼容问题，会按照JDK1.4的语法来对待（JVM会把传递的数组参数拆开，拆开就代表参数的个数不匹配）
解决办法：防止JVM拆开你的数组
	``方式一：``把数组看做是一个Object对象
	``方式二：``重新构建一个Object数组，那个参数数组作为唯一的元素存在。

8. 内省
beanutils内省框架(依赖commons-logging)：apache
www.apache.org

9. 泛型
    只给编译器用的。编译完后字节码中没有泛型的。
    泛型：如果等号左右都用到了泛型，必须类型一致。
    ``ArrayList<Object> list = new ArrayList<String>();``错误的

    只有一边使用泛型，另一边不使用泛型是可以的。

    只有引用类型才能作为泛型方法的实际参数

