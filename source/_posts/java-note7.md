title: Servlet概述
date: 2015-07-23 20:08:53
categories: Java #文章文类
tags: [Java]
---
##一、Servlet概述
1. Servlet是SUN公司制定的一套开发动态网页的技术。
2. JavaEE相关的类，包名一般都是以javax开头

##二、编写第一个Servlet案例应用``重要``
1. 建立一个标准的JavaWeb应用目录
```html
FirstApp:
        WEB-INF:
            classes：
            lib:
            web.xml
```

2. 进入classes目录，建立一个文本文件（所有的Servlet类都必须间接或直接实现javax.servlet.Servlet接口）
    ```java
    package cn.itcast.servlet;

    import java.io.*;
    import javax.servlet.*;

    public class FirstServlet extends GenericServlet{
        public void service(ServletRequest req,
                                 ServletResponse res)
                          throws ServletException,
                                 java.io.IOException{
            OutputStream out = res.getOutputStream();
            out.write("Hello Servlet".getBytes());
            out.close();

        }

    }
    ```
    <!--more-->

3. 进入classes目录，对FirstServlet进行编译：
    前提：把s`ervlet-api.jar`加入到你的构建路径中.`set classpath=%classpath%;C:\apache-tomcat-6.0.35\lib\servlet-api.jar`
    执行：`javac -d . FirsetServlet.java`
4. 修改web.xml，对FirsetServlet进行url地址映射，配置如下：
    ```xml
    <?xml version="1.0" encoding="ISO-8859-1"?>
    <web-app xmlns="http://java.sun.com/xml/ns/javaee"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd"
       version="2.5">
        <servlet>
            <servlet-name>FirstServlet</servlet-name>
            <servlet-class>cn.itcast.servlet.FirstServlet</servlet-class>

        </servlet>
        <servlet-mapping>
            <servlet-name>FirstServlet</servlet-name>
            <url-pattern>/hello</url-pattern>
        </servlet-mapping>
    </web-app>
    ```
5. 把你的应用部署到Tomcat中。
6. 访问地址：`http://localhost:8080/FirstApp/hello`就可以看到写的Servlet类的输出结果了。

## 三、Servlet的生命周期`重要`
容器最终要调用service方法为客户进行服务
1 .Servlet接口中的常用方法：
    - `public void init(ServletConfig config)`：初始化。Servlet类被实例化后就执行，且执行一次。由容器进行调用
    - `public void destroy()`：销毁Servlet对象。由容器进行调用
在内存中一个Servlet只有一个实例。针对不同的用户请求，容器采用多线程的机制调用service方法的。

Servlet实例对象和初始化方法，默认情况下，只有第一次访问时才执行，且只执行一次。

希望在应用被Tomcat加载完毕后（此时还没有任何人访问），就实例化并完成初始化Servlet的工作？
```xml
<servlet>
    <servlet-name>FirstServlet</servlet-name>
    <servlet-class>cn.itcast.servlet.FirstServlet</servlet-class>
    <load-on-startup>2</load-on-startup>
</servlet>
```
## 四. Servlet的孩子们（模板方法设计模式）`重要`
如果设计与HTTP协议有关的Servlet，一般选择集成javax.servlet.http.HttpServlet.
不要覆盖其中的service(ServletRequest req,ServletResponse resp)方法，而应该覆盖掉，doXXX方法。
doXXX就是根据你的请求方式来的。

HttpServlet中的service方法是典型的模板方法设计模式的具体应用。

## 五、Servlet配置
1. 一个Servlet可以被映射到多个URL地址上
2. URL地址映射还支持通配符*
    方式一：以*开头，以扩展名结尾。比如`<url-pattern>*.do</url-pattern>`
    方式二：以/前缀开头，以*结尾。 比如`<url-pattern>/action/*</url-pattern>`
3. 多个Servlet使用通配符时，有可能有多个配置的Servlet，到底执行哪一个？
    原则：优先级
       - 绝对匹配
       - 以"/"开头（方式二）要比"*"开头（方式一）优先级高
       - 都以"/"开头，还是有多个匹配，找最匹配的
4. 如果一个Servlet的映射为一个"/",就称之为默认的Servlet，它负责处理没有映射路径的URL请求的响应。
