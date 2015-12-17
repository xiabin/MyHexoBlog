title: Web会话概述
date: 2015-07-22 20:05:11
categories: Java #文章文类
tags: [Java]
---
## 一、会话概述
1. 会话过程就好比打电话。
2. 学习会话要解决的问题是什么？
   会话过程中的数据保存。

## 二、Cookie和HttpSession简介
* Cookie是客户端技术
* HttpSession是服务器端技术
    <!--more-->

## 三、Cookie详细介绍
  javax.servlet.http.Cookie
1. Cookie是什么？
    * 一个小信息，由服务器写给浏览器的。由浏览器来保存。
    * 客户端保存的Cookie信息，可以再次带给服务器。

2. Cookie的属性：
``name：``必须的
``value:``必须的
``comment:``可选的。注释
``path：``可选的。
写Cookie的程序的访问路径是：``http://localhost:8080/day07/servlet/CookieDemo1``
        其中：localhost就是域名；/day07/servlet就是当前Cookie的path
    访问的地址的URI.startWith(cookie的路径),为true就带

    比如IE存的cookie的路径是/day07
    现在访问的地址是：``http://localhost:8080/day07/servlet/CookieDemo1 `` 带
    现在访问的地址是：``http://localhost:8080/day07/CookieDemo1    ``      带

    比如IE存的cookie的路径是/day07/servlet/
    现在访问的地址是：``http://localhost:8080/day07/servlet/CookieDemo1 `` 带
    现在访问的地址是：``http://localhost:8080/day07/CookieDemo1   ``       不带

    如果一个Cookie的路径设置成了/day07，意味着什么？当前应用下的所有资源浏览器都会带着它给服务器。

    ``domain：``可选的。该Cookie所属的网站域名。（itcast.cn）默认值。
    ``maximum age：``可选的。不设置就是会话过程（存在浏览器的内存中）。单位是秒
      如果是0，说明要删除。
    ``version：``可选的。

3. 如何向客户端写Cookie：HttpServletResponse.addCookie(javax.servlet.http.Cookie)（就是写了一个响应消息头：Set-Cookie:cookie的信息）
特点：一个浏览器针对一个网站最多存20个Cookie；最多存300个Cookie，每个Cookie的长度不能超过4KB。（稀缺）

4. 服务器如何得到客户端传来的Cookie。HttpServletRequest.getCookies()

5. 如何区分Cookie：通过名称不行。
domain+path+name来区分的。
localhost/day07/servlet/lastAccessTime

## 四、Cookie案例：
1. 记住用户最后一次的访问时间
2. 记住用户登录时的用户名
3. 电商网站：记住用户商品的历史浏览记录


## 五、Session案例：
 1.完成用户的一次性登陆（含验证码）
 2.防止表单重复提交：MD5、Base64编码
 3. Session对象的状态转换（生命周期）
 4. 修改HttpSession对象的默认失效时间30分钟  web.xml
 ```xml
 <session-config>
    <session-timeout>1</session-timeout><!--失效时间，单位是分钟-->
 </session-config>
 ```

## 六、浏览器IE，在什么情况下会开启一次新的会话。
 1. IE7(含)-： 开启一个新的浏览器进程，就是一次新会话。
 2. IE8(含)+: 开启一个新的浏览器进程，并没有开始新会话。（文件菜单、开始新会话） 新开启一个Tab页，与原有页面属于同一次会话。 通过一个页面的超链接（target="_blank"），与原有页面属于同一次会话。


## 七、客户端禁用cookie后，会话数据的保存问题
 1. 对于使用``http://localhost``来访问网站，即使客户端禁用了cookie，但是无效。
 2. 解决办法：URL重写。
`http://192.168.1.250:8080/day07/servlet/RegistServlet ----> http://192.168.1.250:8080/day07/servlet/RegistServlet;JSESSIONID=2983749832743298 String url = HttpServletResponse.encodeURL(String url) `
 特点：如果服务器知道客户端没有禁用cookie，那么该方法将不会重写URL。


## 八、各种URL地址的写法
相对路径
绝对路径：（建议的）
绝对路径怎么写？什么时候需要加上应用名称"/day07"?
原则：地址是不是给服务器用的，如果是,"/"就代表着当前应用。如果是给客户端用的绝对路径要加上应用名称。

    <link href=path/>                               要加/day07
    <script src=path/>								要加/day07
    <img src=path/>									要加/day07
    <a href=path/>									要加/day07
    RequestDispatcher.include(path)					不要加，"/"就代表着当前应用
    RequestDispatcher.forward(path)					不要加，"/"就代表着当前应用
    HttpServletResponse.sendRedirect(path)			要加/day07
    ServletContext.getRealPath(path)				不要加，"/"就代表着当前应用
