title: Servlet中概述相关2
date: 2015-07-23 20:18:31
categories: Java #文章文类
tags: [Java]
---
## 一、Servlet中的线程安全问题
	在Servlet中定义变量，除非特殊要求，尽量使用局部变量。
	如果有需要实例变量时，应做同步处理，且同步代码块尽量包围少的代码。

## 二、Servlet的配置对象：
ServletConfig：（容器来创建）
    作用：代表了Servlet配置中的参数信息。
    比如在web.xml中的参数配置如下：
        ```xml
        <servlet>
            <servlet-name>ServletDemo2</servlet-name>
            <servlet-class>cn.itcast.servlet.ServletDemo2</servlet-class>
            <init-param><!-- aaa=bbb -->
                <param-name>aaa</param-name>
                <param-value>bbb</param-value>
            </init-param>
            <init-param>
                <param-name>xxx</param-name>
                <param-value>yyy</param-value>
            </init-param>
          </servlet>
          ```
<!--more-->

## 三、ServletContext详解`重要`
1. 在应用被服务器加载时就创建ServletContext对象的实例。每一个JavaWeb应用都有唯一的一个ServletContext对象
它就代表着当前的应用。
2. 如何得到ServletContext对象：`ServletConfig.getServletContext()`;
3. 有什么用？
    - ServletContext对象是一个域对象（域对象就是说其内部维护了一个Map<String,Object>）
        `Object getAttribute(String name)`:根据名称获取绑定的对象
        `Enumeration getAttributeNames() `:获取ServletContext域中的所有名称
        `void removeAttribute(String name)`:根据名称移除对象
        `void setAttribute(String name,Object value)`:添加或修改对象。
    - 实现多个Servlet之间的数据共享
    - 获取WEB应用的初始化参数（应用的全局参数）
        在web.xml的根元素下配置一下信息：
        ```xml
        <context-param>
            <param-name>encoding</param-name>
            <param-value>UTF-8</param-value>
        </context-param>
        ```
        这些参数就属于整个应用的全局参数，使用ServletContext来读取。
    - 读取资源文件的三种方式：
        利用ServletContext.getRealPath():
        　　特点：读取应用中任何文件。只能在Web环境下用
        利用ResourceBundle读取配置文件
        　　特点：可以用在非web环境下。但是只能读取类路径中的properties文件
        利用类加载器读取配置文件（专业）
        　　特点：可以用在非web环境下。可以读取类路径下的任何文件。
    插入：ServletAPI中的主要接口或类的核心类图
    Tips:更改MyEclipse生成的Servlet的模板（8.5）
    1. 关闭你的MyEclipse
    2. 找到MyEclipse的安装目录C:\Program Files\Genuitec
    3.搜索*wizard*(com.genuitec.eclipse.wizards_8.5.0.me201003052220.jar)
    4. 打开，找到templates\Servlet.java,这个文件就是MyEclipse生成Servlet的模板代码
    5. 弄出来，做好备份，开始修改。

## 四、请求和响应对象概述
HTTP协议包含请求和响应部分。
HttpServletRequest就代表着请求部分
HttpServletResponse就代表着响应部分

**学好的关键：HTTP协议。**

## 五、HttpServletResponse详解｀重要`
	1. 输出中文数据：
		字节流：
            out.write("中文".getBytes("UTF-8"));有乱码
            解决办法：
                方式一：更改浏览器的查看编码（不可取）
                通知浏览器，使用的码表
                方式二：response.setHeader("Content-Type", "text/html;charset=UTF-8");
                方式三：response.getOutputStream().write("<meta http-equiv='Content-Type' content='text/html;charset=UTF-8'>".getBytes("UTF-8"));
                *方式四：response.setContentType("text/html;charset=UTF-8");//方式二、三、四都是一样的

		字符流：
			Servlet中的字符流默认查ISO-8859-1（SUN的Servlet规范要求的）
			如何更改这个默认的编码呢？response.setCharacterEncoding("UTF-8");

			//不要忘记通知浏览器的编码
			`response.setCharacterEncoding("UTF-8");`
			`response.setHeader("Content-Type", "text/html;charset=UTF-8");`
			`PrintWriter out = response.getWriter();`
			`out.write(s);`//默认查的是ISO-8859-1码表（SUN的Servlet规范要求的）


			在字符流输出中文数据时：`response.setContentType("text/html;charset=UTF-8");`
					有两个作用：通知字符流以`UTF-8`编码输出
								通知客户端以`UTF-8`解码显示


	2. 控制不要缓存
	3. 控制缓存时间
	4. 动态生成随机验证码图片
	5. 定时刷新
	6. 文件下载（中文文件名的文件下载）
	7. HttpServletResponse细节:
		字节流和字符流不能同时使用，互斥的。
		通过字符流或字节流输出的数据并不是直接打给浏览器的。而是把数据写到response对象的缓存中的。服务器从缓存中取出数据，按照HTTP协议的响应格式输出给浏览器。
		如果你调用的response的输出流没有主动关闭，服务器会替你关的。

## 六、HttpServletRequest详解｀重要`
	代表着客户端的请求。要客户的信息只要找这个对象即可，该对象由容器创建。
	学习关键：时刻记住HTTP协议的请求部分的具体内容。

	1. 常用简单方法
	2. 获取请求消息头
	3. 获取请求参数（内省）
	4. 常用表单数据的获取
		表单输入域类型：
			radio checkbox,即使表单中有对应名称输入域，如果一个不选择，则什么数据不会带给服务器。（注意空指针异常）
			如果选择了其中的一个或多个，则把他们的value的取值提交给服务器。
			如果选择了其中的一个或多个，他们又没有value取值，则提交给服务器的值是on.
		请求参数的编码：
			浏览器当前使用什么编码，就以什么编码提交请求参数。`<meta http-equiv="content-type" content="text/html; charset=UTF-8">`

			`request.setCharacterEncoding(编码)`:通知程序，客户端提交的数据使用的编码。但是只对POST请求方式有效

			如果是get请求提交数据，编码就是ISO-8859-1

		Tips：目前采用POST提交方式。

	5. 域对象：
		ServletRequest也是一个域对象（内部维护了一个Map<String,Object>）
			`Object getAttribute(Stirng name):`
			`void setAttribute(String name,Object value):`
			`void removeAttribute(String name):`

	6. 请求转发和重定向
		请求转发借助于RequestDispatcher
		如何得到RequestDispatcher对象：
			方式一：ServletContext.getRequestDispatcher(目标资源的URI);
			方式二：ServletRequest.getRequestDispatcher(目标资源的URI);
			区别：
				方式一中的目标资源的URI必须以"/"开头，否则报错，此"/"就表示的是当前应用（绝对路径表示法）
				方式二中的目标资源的URI如果以"/"开头，就表示的是当前应用（绝对路径表示法）。如果不以"/"开头，就表示相对路径。


	7. 转发和重定向细节（实际开发中知道一个原则）`了解原则`
		请求转发：由源组件转发到目标组件时，容器会清空源组件输出的数据。因此，用户只会看到目标组件输出的页面结果。
					但是，响应头信息是不清空的。
		编码原则：不要在转发前后向页面输出数据，也不要关闭输出流。（做无用功）
	8. 包含：（动态包含）`了解原则`
		由源组件包含到目标组件时，容器会清空目标组件的头。因此，源组件设置的头才有效。
					但是，响应体信息是不清空的。
		编码原则：不要在目标组件中设置响应头。（做无用功）
