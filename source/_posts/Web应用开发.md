---
title: Web应用开发
cover: https://ts1.cn.mm.bing.net/th/id/R-C.363458fe9ff9196cbb7426ff61466033?rik=F%2bwoW8yrX3An0w&riu=http%3a%2f%2fp7.qhmsg.com%2fdr%2f270_500_%2ft01bedc70ec43d9d0ac.jpg&ehk=3nP941eRQPyTA%2fLrZXxy6Xx5Ek09rI06uQYE7sOZawA%3d&risl=&pid=ImgRaw&r=0
author: Veni
categories: notes
keywords: Servlet JSP
tags:
  - Java
date: 2023-12-17 20:23:50
---

Web应用开发的课程笔记。<!--more-->

# Web应用开发

## Web基础知识

### HTML基本概念

- DOM：文档对象模型，是独立于平台的内容和结构。一个HTML的所有元素的表示模型就是一个DOM，表现为一个多层级的树

![image-20231217203929130](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312172039300.webp)

- BOM：浏览器对象模型，独立于内容且可以和浏览器交互的对象。

BOM的层次如下：

<img src="https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/ecec85dcf1ff4f27a815685189feaccf~tplv-k3u1fbpfcp-zoom-in-crop-mark:1512:0:0:0.awebp">

### HTTP协议

> 协议组成，常见状态码，常用方法

HTTP工作三大特点：无连接，媒体独立，无状态的

![image-20231217202900899](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312172029094.webp)

八种请求类型：GET, POST, PUT, DELETE, OPTIONS, HEAD, TRACE, CONNECT

> HTTP请求：由请求行（请求方式、资源路径、所用HTTP版本），请求头，实体内容
>
> HTTP响应：由响应状态行、响应头、实体内容

### CSS基础

四种引入方式：行内式、内嵌式、外链式、导入式。

![image-20231220011604673](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312200116795.webp)

> 外链式和导入式都是引入外部CSS代码的方法，异同在于：
>
> 外链式：使用\<link href="">标签引入，导入式使用@引入，例如\<style>@import "css/hello.css"\<style>

代码：

```html
<html>
<title>这是一个测试</title>
<head>
    <link href="css/myCss.css" type="text/css" rel="stylesheet">
</head>
<body>
<h1 style="color: blue">行内式写法：</h1>
<p>在标签后面加上 style="属性:值"</p>
<p>例如：style="color: blue"</p>
<h1> 内嵌式：将代码集中写在head标签中</h1>
<p>
    &lt;head&gt;
    &lt;style type="text/css"&gt;
            h1{ text-align: center}
    &lt;/style&gt;
    &lt;/head>
</p>
<h1>外链式</h1>
</body>
</html>
```

### js基础

js的三种引入方式：行内式、内嵌式和外链式。

```jsp
<head>
    <link href="css/myCss.css" type="text/css" rel="stylesheet">
    <title>测试</title>
    <script type="text/javascript" src="js/my.js"></script>
    <script type="text/javascript">
        function neiqian(){
            alert("这是内嵌式写的")
        }
    </script>
</head>
```

## Servlet教程

### 第一个Servlet

使用一个自己实现的Servlet类实现HttpServlet，然后重写doGet，doPost，doPut，每当有HTTP请求到达对应的路径的时候，就会调用对应的方法。

```java 
//Servlet运行测试
@WebServlet(name = "/TestServlet0", urlPatterns = {"/hello"})
public class Servlet01 extends HttpServlet {
    @Override
    public void doGet(HttpServletRequest request, HttpServletResponse response) throws IOException {
        response.setContentType("text/html");
        PrintWriter out = response.getWriter();
        out.println("<h1>Test Servlet 0</h1>");
        out.close();
    }
}
```

### Servlet的配置

使用web.xml可以对Servlet进行配置，步骤如下：

1. 使用\<servlet\>标签进行注册，在该标签下包含若干个元素，如\<servlet-name>,\<servlet-class>等。
2. 把Servlet映射到URL地址，使用\<servlet-mapping>标签定义映射，使用\<servlet-name>指定需要映射的Servlet，然后使用\<url-pattern>子标签进行URL地址，地址前必须加"/"。

例如，我们把上面的@WebServlet配置去掉，改用web.xml配置，实现如下：

注意，servlet必须配置name和class两个参数，class用于指定实现类，name用于和mapping建立映射。

```xml
  <servlet>
    <servlet-name>TestServlet01</servlet-name>
    <servlet-class>Servlet01</servlet-class>
  </servlet>

  <servlet-mapping>
    <servlet-name>TestServlet01</servlet-name>
    <url-pattern>/hello</url-pattern>
  </servlet-mapping>
```

> 使用注解配置时，value和urlPatterns通常是二选一，通常是忽略value，使用urlPattern，见文知意。

### Servlet生命周期

Servlet有三个生命周期：初始化，运行和销毁。

要想测试生命周期，必须实现GenericServlet，重写init()，service()和destroy接口。

- 当第一次访问/Servlet02对应的Servlet的时候，init()函数会被调用
- 每一次访问该页面，都会调用service()服务（包括刷新）
- 当停止程序的时候，调用destroy()

```java 
@WebServlet(name = "/TestServlet02", urlPatterns = {"/Servlet02"})
public class Servlet02 extends GenericServlet {
    @Override
    public void init() throws ServletException {
        System.out.println("init");
    }

    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("hello");
    }

    @Override
    public void destroy() {
        System.out.println("destroy");
    }
}
```

### ServletConfig

#### 配置环境

在Servlet运行期间，常常需要一些配置，例如编码等等。使用ServletConfig类可以对Servlet进行初始化和默认参数的管理。例子如下：

```java 
@WebServlet(name = "/Servlet03", urlPatterns = {"/Servlet03"},initParams = {
        @WebInitParam(name = "encoding", value = "UTF-8")
})
public class Servlet03 extends HttpServlet {

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        PrintWriter out=httpServletResponse.getWriter();
        ServletConfig config=this.getServletConfig();
        String param = config.getInitParameter("encoding");
        out.println("encoding="+param);
    }
}
```

HttpServlet类中有一个getServletConfig方法，返回的是一个ServletConfig类的对象。上述例子使用了注解@WebInitParam方法实现了参数初始化，将encoding设置为UTF-8。除了上述方法外，还可以使用web.xml配置参数。例子如下：

首先配置xml的参数：注意context-param要写在servlet之前

```xml
<web-app>
  <display-name>Archetype Created Web Application</display-name>
    
  <context-param>
    <param-name>encoding</param-name>
    <param-value>UTF-8</param-value>
  </context-param>
    
  <context-param>
    <param-name>param2</param-name>
    <param-value>value2</param-value>
  </context-param>
    
  <servlet>
    <servlet-name>TestServlet01</servlet-name>
    <servlet-class>Servlet01</servlet-class>
  </servlet>
</web-app>
```

然后获取xml中的配置并打印：
```java 
@WebServlet(name = "Servlet04",urlPatterns = "/servlet04")
public class Servlet04 extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        res.setContentType("text/html;charset=utf-8");
        PrintWriter out=res.getWriter();
        ServletContext context=this.getServletContext();
        Enumeration<String> paramsNames=context.getInitParameterNames();
        out.println("参数是");
        while(paramsNames.hasMoreElements()){
            String name = paramsNames.nextElement();
            String value=context.getInitParameter(name);
            out.println(name+":"+value);
            out.println("<br/>");
        }
    }
}
```

执行结果如下：

![image-20231219000256150](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312190003311.webp)

获取路径：HttpServlet-> ServletContext-> getInitParameterNames获取names的枚举值，然后调用context.getInitParameter。

#### 多个Servlet共享数据

首先写一个Servlet05，重写doGet请求，调用context.setAttribute设置上下文属性。

```java 
@WebServlet(name = "Servlet05",urlPatterns = "/servlet05")
public class Servlet05 extends HttpServlet {

    public void doGet(HttpServletRequest req, HttpServletResponse res){
        ServletContext context=this.getServletContext();
        context.setAttribute("data","this is a data from me");
    }
}
```

然后写一个Servlet06，获取attribute，

```java 
@WebServlet(name = "Servlet06",urlPatterns = "/servlet06")
public class Servlet06 extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        PrintWriter out = res.getWriter();
        String data=(String) this.getServletContext().getAttribute("data");
        out.println(data);
    }
}
```

先调用servlet05，再调用servlet06，运行结果如下图：

![image-20231219002124686](C:/Users/19481/AppData/Roaming/Typora/typora-user-images/image-20231219002124686.png)

#### 读取Web应用下的资源文件

资源文件包括.properties文件等。示例代码如下

```java 
@WebServlet(name = "Servlet07",urlPatterns = "/servlet07")
public class Servlet07 extends HttpServlet {
    public void doGet(HttpServletRequest req, HttpServletResponse res) throws IOException {
        ServletContext context = this.getServletContext();
        res.setContentType("text/html,charset=utf-8");
        PrintWriter out= res.getWriter();
        InputStream in = context.getResourceAsStream("veni.properties");
        Properties properties=new Properties();
        properties.load(in);
        out.println("Name="+properties.getProperty("Name"));
        out.println("City="+properties.getProperty("City"));
    }
}
```

### HttpServletResponse

类图如下，不作过多讲解。

![image-20231219011108143](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312190111467.webp)

获取请求信息和请求头就直接调函数即可，下面讲讲请求重定向。

- 请求重定向：两次请求，请求不同的资源

### HttpServletRequest

类图如下，根据函数名即可作用。

![image-20231219010924558](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312190109846.webp)

- 请求转发：一次请求，通过ServletContext共享。

### Filter

Servlet规范有三个高级特性，Filter，Listener，以及文件的上传下载。Filter用于修改request和response，Listener用于监听context, session, request事件。

![Use of filters in Servlet](https://th.bing.com/th/id/R.1cc9c9543e2604300e2181353caa438a?rik=aMy%2ffSlsWHGavw&riu=http%3a%2f%2ftutorials.jenkov.com%2fimages%2fjava-servlets%2fservlet-filters-1.png&ehk=6s4rd3wKOrfdMwhy537QCgFAA8ohUM6Znq00xw92x84%3d&risl=&pid=ImgRaw&r=0)

Servlet Filter是过滤器，由多个Filter组成Filter链，只有通过所有过滤器的请求才能进入Servlet。

> 拦截器是Servlet内部的处理方法，而Filter是Servlet外部的拦截器。

- Filter接口

该接口定义了init()，doFilter()方法和destroy()方法。

- FilterConfig接口

在Filter初始化的时候，服务器将FilterConfig对象作为参数传递给Filter初始化方法。

- FilterChain接口

该接口的doFilter()方法调用Filter，

#### Filter生命周期

**创建阶段->执行阶段->销毁阶段**

#### 实现Filter

使用@WebFilter配置需要拦截的资源，实现如下：

```java 
@WebFilter("/filter")
public class MyFilter implements Filter {
    // 如果路径匹配，就会执行这个方法
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        servletResponse.getWriter().println("Filter");
        filterChain.doFilter(servletRequest, servletResponse);
    }
}
```



### Listener

在Web开发中，经常需要对某些事件进行监听。为此，Servlet提供了监听器，专门监听Servlet事件。

监听过程的组成部分：

1. 事件：用户的操作，如创建对象，调用方法，创建对象等。
2. 事件源：产生事件的对象。
3. 事件监听器
4. 事件处理器

事件监听器的过程分为以下步骤

1. 注册监听器：将监听器绑定到事件源
2. 监听器监听到事件发生，会调用监听器的成员方法
3. 事件处理器通过实践对象获得事件源，并对事件源进行处理

例如，实现一个自动插入审计字段的函数，当执行mapper的时候，调用Listener处理，更新时间等。

例如，一个监听器的实现如下：
```java 
@WebListener
public class MyListener implements ServletContextListener, HttpSessionListener, ServletRequestListener {
    public void requestDestroyed(javax.servlet.ServletRequestEvent sre) {
        System.out.println("requestDestroyed");
    }
    public void requestInitialized(javax.servlet.ServletRequestEvent sre) {
        System.out.println("requestInitialized");
    }
}
```



## 会话技术

### Cookies

- 使用Cookie类，该类仅有一个构造函数：参数为(String name, String value)。一个cookie就是一个带有过期时间等属性的KV对。

- 获取请求的cookies：使用HttpServletRequest类的getCookies()，返回Cookie[]，见上面的类图。
- 设置响应的cookie：使用HttpResponse的addCookie，见类图。注意这里的add不会叠加，只是在返回时更新浏览器的本地cookies。

### Session技术

当浏览器访问Web服务的时候，Servlet容器就会创建一个Session对象和ID属性。Session对象就相当于病例档案，而SessionId就是病例档案号。

Session只会在用户第一次访问JSP，Servlet等应用的时候才会创建。

- Session的实现为HttpSession，是一个Java Interface，定义如下图：

![image-20231219101738007](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312191017177.webp)

可以看到，里面有一个servletContext的成员变量，而interface中的变量都是final的，因此该context不可重新赋值。由于Servlet默认启用session技术，所以在浏览器中的cookies栏都会有一个JSESSIONID。

- HttpServletRequest方法中定义了一个获取Session对象的getSession()函数用于返回当前请求的HttpSession对象。有两个重载形式：
	```java 
	public HttpSession getSession(boolean create);
	public HttpSession getSession();
	//实际上就是默认参数
	public HttpSession getSession(boolean create=true);
	```

	如果create为true，那么在HttpSession对象不存在的时候创建一个新的HttpSession，为false返回null。第二个方法默认为true。

## JSP

JSP知识点如下：

![JSP](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312191913166.webp)

### 基本语法

基本声明和包导入，使用contentType声明文件类型等信息，一般而言IDE会自动完成

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page import="java.util.Date" %>
```

基本结构：声明和引入完成之后，就是html主体,包括html标签下的head和body等标签。一个完整的jsp如下：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<%@ page import="java.util.Date" %>
<%@ page import="java.text.SimpleDateFormat"%>

<html>
<head>
    <title>JSP显示系统时间</title>
</head>
<body>
    <%
        Date date = new Date();
        SimpleDateFormat df = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
        String today= df.format(date);
    %>
    当前时间：<%=today%>
</body>
</html>
```

### JSP脚本元素

#### JSP Scriptlets

Scriptlets就是一个代码段，可以编写java的程序语句。使用格式如下。在Scriptlets中可以进行属性的定义，也可以输出内容，但是无法声明函数。

```jsp
<% java code %>
```

声明函数的语法如下：

```jsp
<%! java code %>
```

在声明代码段中的函数在整个JSP页面都可以生效，包括多次打开和关闭，除非关闭服务器。



### JSP指令

JSP指令用于配置页面中的一些信息。例如

- page指令：用于指定某个jsp页面的属性和引入java包。常用的page属性有：language，import，contentType。

```jsp
<%@ page import="java.util.Date" %>
```

- include指令：实际开发中，有时候会要在一个jsp页面中包含另一个jsp页面，这时候可以通过include指令实现。Include一般使用相对路径。
- taglib指令：可以使用该指令标识页面中所使用的标签库。

### JSP动作元素

- \<jsp:include page="" flush="">用于向当前页面引入其他页面的文件，<% @include file = "" %>，动态与静态的区别。
- \<jsp:forward>将当前请求转发到其他的Web资源，类似Servlet中的request.forward()，因为JSP本质上也是一个Servlet。

### JSP隐式对象

JSP有9个隐式对象，分别是：out, request, response, config, session, application, page, pageContext, exception。

## EL和JSTL

### EL

Expression Language，语法格式。

EL的核心格式就是"${}"

EL提供了11个隐式对象，这些对象类似于JSP的隐式对象

### JSTL

Java server pages standard tag library，JSP标准标签库，用于替代Java的部分关键字，在页面中处理部分业务逻辑。

JSTL必须使用JSP的taglib指令来指定标签库。标签库本质上是取代在html中嵌入Java代码的，因此具备逻辑处理能力。

> JSTL包含五类标准标签库，分别是核心标签库、国际化/格式化标签库、SQL标签库、XML标签库和函数标签库。
>
> 简记为：核、国、S、X、函。

- \<c:out value="" default="">标签\</c:out>，用于输出元素。

## JDBC

常用API：

- Driver接口：所有JDBC程序必须实现的接口，专门给数据库厂商使用。
- DriverManager类：用于加载JDBC驱动并且建立与数据库的连接。有两个比较重要的静态方法：

1. registerDriver(Driver driver);注册数据库驱动
2. getConnection(String url, String user, String pwd);建立连接返回Connection对象。

连接数据库：
```java 
Class.forname("com.mysql.cj.jdbc.Driver");
String url = "jdbc:mysql://localhost:3306/my_db?serverTimezone=GMT%2B8";
conn = DriverManager.getConnection(url,username,password);
```

这样一个连接就建立完成了。

- Statement接口：用于执行静态的SQL语句

```java 
stmt = conn.createStatement();
ResultSet rs=stmt.excute("select * from users");
while(rs.next()){
    int id=GetInt("id");
    String name=GetString("name");
    // ...
}
```

- ResultSet：用于保存查询结果，可以保存单条或多条，使用rs.next()遍历。

- PreparedStatement接口：封装了JDBC执行SQL语句的方法，完成Java调用SQL的功能。

## Spring

### Spring的优良特性

- 非侵入式：基于Spring开发的应用程序对象可以不依赖于Spring的API。举个例子，使用Spring initializr初始化了一个Spring项目，但是可以不使用Spring风格的注解写Controller，可以使用Servlet写。
- 控制反转IoC：将对象的创建权交给Spring，以前都是我们手动创建对象，现在是由Spring创建。常常使用容器管理，这些容器使用反射、配置文件或注解来定义对象之间的依赖关系，并提供了自动注入依赖的机制。例如，使用@Bean之类的方法控制反转，类会成为一个Bean，当有需要的时候，会自动创建（如@Resource，@Autowired等。

```java 
@Configuration
public class AppConfig {
    @Bean
    public UserRepository userRepository() {
        return new UserRepositoryImpl();
    }
	//当注册为Bean的时候，可以在其他地方有需要时自动引入。
    @Bean
    public UserService userService(UserRepository userRepository) {
        return new UserService(userRepository);
    }
}
```

- 依赖注入DI：实现控制反转的一种方法，指的是依赖的对象不需要手动调用set方法设置，而是通过配置赋值。例如Spring的JDBC连接，将对应的配置文件写好，然后在任何地方都可以调用@Mapper等方法实现。
- 面向切面编程AOP：用于将横切关注点（cross-cutting concerns）与核心业务逻辑分离。横切关注点指的是那些在应用程序中多处重复出现的功能，例如日志记录、事务管理、安全性检查等。例如，可以使用AOP的思想自动设置时间等审计字段的更新。

- 容器化与组件化：容器化是指Spring包含并管理对象的生命周期，组件化是指通过各种组件配置组合形成一个复杂的应用，使用xml或注解组合对象。
- 一站式：可以整合各种框架和第三方库，实际上Spring自身也提供了表述层的SpringMVC和持久层的Spring JDBC。

> 表述层是指应用程序中处理用户请求和生成响应的部分。它负责将用户的输入转化为可理解的数据，并将处理结果以适合用户的方式进行呈现。

Spring 可以使开发人员使用 **POJOs** 开发企业级的应用程序。只使用 POJOs 的好处是你不需要一个 **EJB** 容器产品（企业级JavaBeans），比如一个应用程序服务器，但是你可以选择使用一个健壮的 servlet 容器，比如 Tomcat 或者一些商业产品。

> POJO是"Plain Old Java Object"的缩写，它是一种简单的Java对象，没有任何特殊要求或限制。POJO是一种纯粹的普通Java对象，不依赖于任何特定的框架、接口或类库。

### Spring框架体系结构

![image-20231219214854914](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312192150920.webp)

最下层是Test模块，然后是Core Container模块。

![image-20231219215119695](https://vblog-1315512378.cos.ap-guangzhou.myqcloud.com/imgs/vblog/202312192151924.webp)

