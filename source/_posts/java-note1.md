title: JSP概述
date: 2015-07-23 20:10:36
categories: Java #文章文类
tags: [Java]
---

## 一、JSP概述
1. JSP:Java Server Pages(运行在服务器端的页面)。就是Servlet。
学习JSP学好的关键：时刻联想到Servlet即可。
2. JSP的原理<br/>
   JSP会被Tomcat翻译成一个Servlet。该Servlet在Tomcat\work\catalina\locahot\day09\....
3. JSP、Servlet开发的最佳实践:
    <br>Servlet：一般作为控制组件，处理业务逻辑
    <br>JSP：一般作为显示组件，显示数据
  <!--more-->

## 二、JSP的语法
1. JSP的模板元素：(先写HTML)
<br>就是JSP中的那些HTML标记
<br>作用：页面布局和美化
2. JSP的Java脚本表达式：
<br>作用：输出数据到页面上
<br>语法：``<%=表达式%>``(实际上就是调用输出流打印到页面上)
3. JSP中的Java脚本片段：(实际开发中，应做到JSP中不能出现一行Java脚本片段)
<br>作用：书写Java代码逻辑
<br>语法：
```javascript
<%
	语句1;
	语句2;
 %>
```
<br>原理：其中的语句会原封不动的被服务器翻译到对应的Servlet的service方法中。
4. JSP的声明：（了解其原理）
<br>作用：定义类的成员
<br>语法：
```javascript
<%!
    你的Java代码
%>
```
5.JSP的注释：
<br>作用：注释Java脚本代码
<br>语法：``<%--这是注释--%>``

## 三、JSP的指令:给JSP引擎用的（服务器用的）
基本的语法格式：``<%@ 指令名称 属性1="值1" 属性2="值2" ....%>``
<br>作用：告诉服务，该如何处理JSP中除了指令之外的内容的。<br>

1. **page**
<br>作用：定义JSP页面的各种属性
<br>属性：<br>
    ``language:``指示JSP页面中使用脚本语言。默认值java，目前只支持java。
    ``extends：``指示JSP对应的Servlet类的父类。不要修改。
	<br>``import：``导入JSP中的Java脚本使用到的类或包。（如同Java中的import语句）
	SP引擎自动导入以下包中的类：
    ``javax.servlet.*``
    ``javax.servlet.http.*``
	``.servlet.jsp.*``
		注意：一个import属性可以导入多个包，用逗号分隔。
	``sessioin:``指示JSP页面是否创建HttpSession对象。默认值是true。可选值true|false.
    ``buffer：``指示JSP用的输出流的缓存大小.默认值是8Kb。
	``autoFlus：``自动刷新输出流的缓存。
	``isThreadSafe：``指示页面是否是线程安全的（过时的）。默认是true。
		true：不安全的。
		false：安全的。指示JSP对应的Servlet实现SingleThreadModel接口。
	``errorPage:``指示当前页面出错后转向（转发）的页面。
	目标页面如果以"/"（当前应用）就是绝对路径。

    配置全局错误提示页面：
	web.xml
```xml
<error-page>
	<exception-type>java.lang.Exception</exception-type>
    <location>/error.jsp</location>
</error-page>
<error-page>
	<error-code>404</error-code>
	<location>/404.jsp</location>
</error-page>
```

    ``isErrorPage:``指示当前页面是否产生Exception对象。<br>
	``contentType：``指定当前页面的MIME类型。作用与Servlet中的response.setContentType()作用完全一致
	<br>``pageEncoding：``通知引擎读取JSP时采用的编码（因为要翻译）
	还有contentType属性的作用。<br>
	``isELIgnored:``是否忽略EL表达式。${1+1}。默认值是false。

 page指令最简单的使用方式：``<%@ page pageEncoding="UTF-8"%>``
2. **include**（静态包含,开发中能用静的不用动的）
	<br>作用：包含其他的组件。
	<br>语法：<%@include file=""%>file指定要包含的目标组件。路径如果以"/"（当前应用）就是绝对路径。
	<br>原理：把目标组件的内容加到源组件中，输出结果。

	动态包含：
	采用动作元素：<jsp:include page=""/>路径如果以"/"（当前应用）就是绝对路径。

3.  **taglib**<br>
    作用：引入外部的标签<br>
	语法：
```javascript
<%@taglib uri="标签名称空间" prefix="前缀"%>
<%@ taglib uri="http://java.sun.com/jsp/jstl/core" prefix="c"%>
```


## 四、JSP的内置对象（9个JSP内置对象）
    JSP的内置对象引用名称			对应的类型

    request							HttpServletRequest
    response						HttpServletResponse
    session							HttpSession(有开关的：page指令的session属性的取值)
    application						ServletContext
    config							ServletConfig
    page							this(当前Servlet对象)
    exception						java.lang.Throwable（有开关的：page指令的isErrorPage属性改为true）

    out								JspWriter
    pageContext						javax.servlet.jsp.PageContext非常重要
有三大作用：

1. 本身是一个域对象。同时还能操作其他三个域对象（PageContext ServletRequest HttpSession  ServletContext）
	<br>本身表示的域范围是本页面。    <br>
```java
void setAttribute(String name,Object value)
void removeAttribute(String name)
Object getAttribute(String name)
```

	操作其他的三个域对象  <br>
```java
void setAttribute(String name,Object value,int scope)
void removeAttribute(String name,int scope)
Object getAttribute(String name,int scope)
```
    参数int scope是由PageContext类提供的静态变量规定的。
	``PageContext.PAGE_SCOPE:``页面范围（是PageContext本身中的那个Map，代号page）  <br>
	``PageContext.REQUEST_SCOPE:``请求范围（是ServletRequest中的那个Map，代号request）  <br>
	``PageContext.SESSION_SCOPE:``请求范围（是HttpSession中的那个Map，代号session）  <br>	PageContext.APPLICATION_SCOPE:请求范围（是ServletContext中的那个Map，代号application）  <br>
    ``（非常有用）Object findAttribute(String name):``依次按照page request session application范围搜索指定名称的对象，找到为止。  <br>

2. 获取其他8个隐式对象
3. 提供了转发和包含的方便方法
```java
RequestDispatcher rd = request.getRequestDispatcher("/url");
rd.forward(request,response);
pageContext.forward("url");
pageContext.include("url");
```

## 五、四大域对象(两个资源之间互传数据)

| JSP中隐式对象的名称        | 范围名称           | 具体的类型  |
| ------------- |:-------------:| -----:|
| pageContext | page	 | javax.servlet.jsp.PageContext |
| request	  | request	 | javax.servlet.ServletRequest  （显示完数据就没有用了） |
| session	  | session	 | javax.servlet.http.HttpSession (显示完数据了，过一会自己还要用) |

## 六、JSP常用的动作元素
	<jsp:include/>
	<jsp:forward/>
	<jsp:param/>:在包含和转发时，利用该标签传递请求参数




