## Servlet

### 什么是 Servlet：

1. Servlet 是 JavaEE 规范之一。规范就是接口
2. Servlet 就 JavaWeb 三大组件之一。三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监听器。
3. Servlet 是运行在服务器上的一个 java 小程序，它可以接收客户端发送过来的请求，并响应数据给客户端。



### 实现servlet：

1. 编写一个类去实现 Servlet 接口
2. 实现 service 方法，处理请求，并响应数据
3. 到 web.xml 中去配置 servlet 程序的访问地址

```java
package com.chr.servlet;

import jakarta.servlet.*;

import java.io.IOException;

/**
 * @Author: 程浩然
 * @Create: 2024/7/13 - 23:32
 * @Description:
 * Servlet接口第一次尝试
 */
public class HelloServlet implements Servlet {
    @Override
    public void init(ServletConfig servletConfig) throws ServletException {

    }

    @Override
    public ServletConfig getServletConfig() {
        return null;
    }

    /**
     * service 就是专门用来处理请求和响应的
     * @param servletRequest
     * @param servletResponse
     * @throws ServletException
     * @throws IOException
     */
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("hello servlet 被访问了");
    }

    @Override
    public String getServletInfo() {
        return null;
    }

    @Override
    public void destroy() {

    }
}

```

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="https://jakarta.ee/xml/ns/jakartaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="https://jakarta.ee/xml/ns/jakartaee https://jakarta.ee/xml/ns/jakartaee/web-app_6_0.xsd"
         version="6.0">


    <servlet>
        <!--        servlet-name 标签：Servlet程序起一个别名（一般是类名）-->
        <servlet-name>helloServlet</servlet-name>
        <!--        Servlet-class 标签：Servlet程序的全类型-->
        <servlet-class>com.chr.servlet.HelloServlet</servlet-class>
    </servlet>

    <!--    servlet-mapping 标签给Servlet 程序配置访问地址-->
    <servlet-mapping>
        <!--        servlet-name 告诉服务器，当前配置的地址给那个servlet程序使用-->
        <servlet-name>helloServlet</servlet-name>
        <!--        url-pattern 配置访问地址-->
        <!--        / 斜杠在服务器解析时候，表示地址为：http://ip:port/工程路径-->
        <!--        /hello 表示地址：http://ip:port/工程路径/hello-->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
</web-app>
```

> 当访问`/hello 表示地址：http://ip:port/工程路径/url-pattern值`时就会执行service方法

> servlet 标签中定义了全类名和他的别名，servlet-mapping定义了url和执行程序的别名的关系

### servlet实现过程：

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240714001618244.6f0lsb27r1.webp)

1. 通过localhoot，8080，/servlet 找到对应服务器对应端口对应工程
2. 已知资源路径，优先在web.xml配置文件中找对应的`<servlet-mapping>`标签的资源路径名即对应的servlet类名，通过servlet类名，在`<servlet>`标签中找到`<servlet-class>`，即为这个资源路径对应的包名加类名。



### servlet 程序的生命周期：

1. 执行 Servlet 构造器方法
2. 执行 init 初始化方法

3. 执行 service 方法

4. 执行 destroy 销毁方法



第一、二步，是在第一次访问，的时候创建 Servlet 程序会调用。

第三步，每次访问都会调用。

第四步，在 web 工程停止的时候调用。



### servlet 请求的分别处理：

```java
@Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        System.out.println("3 service === Hello Servlet 被访问了");
        // 类型转换（因为它有 getMethod()方法）
        HttpServletRequest httpServletRequest = (HttpServletRequest) servletRequest;
        // 获取请求的方式
        String method = httpServletRequest.getMethod();
        if ("GET".equals(method)) {
            doGet();
        } else if ("POST".equals(method)) {
            doPost();
        }
    }

    /**
     * 做 get 请求的操作
     */
    public void doGet() {
        System.out.println("get 请求");
        System.out.println("get 请求");
    }

    /**
     * 做 post 请求的操作
     */
    public void doPost() {
        System.out.println("post 请求");
        System.out.println("post 请求");
    }
```



### 通过继承 HttpServlet 实现 Servlet 程序:

一般在实际项目开发中，都是使用继承 HttpServlet 类的方式去实现 Servlet 程序。

1. 编写一个类去继承 HttpServlet 类
2. 根据业务需要重写 doGet 或 doPost 方法
3. 到 web.xml 中的配置 Servlet 程序的访问地址

```java
package com.chr.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

/**
 * @Author: 程浩然
 * @Create: 2024/7/14 - 1:28
 * @Description: 继承 HttpServlet 类实现 servlet
 */
public class HelloServlet2 extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("post");
    }

    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("get");
    }
}
```





### Servlet 类的继承关系：

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240714104952188.8z6g4y26db.webp)

### ServletConfig 类：

三个作用：

1. 可以获取 Servlet 程序的别名 Servlet-name 的值
2. 获取初始化参数 init-param
3. 获取 ServletContext对象

```xml
<servlet>
        <!--        servlet-name 标签：Servlet程序起一个别名（一般是类名）-->
        <servlet-name>helloServlet</servlet-name>
        <!--        Servlet-class 标签：Servlet程序的全类型-->
        <servlet-class>com.chr.servlet.HelloServlet</servlet-class>


        <!--初始化信息-->
        <init-param>
            <!--是参数名-->
            <param-name>url</param-name>
            <!--是参数值-->
            <param-value>jdbc:mysql://localhost:3306/test</param-value>
        </init-param>
    </servlet>


    <!--    servlet-mapping 标签给Servlet 程序配置访问地址-->
    <servlet-mapping>
        <!--        servlet-name 告诉服务器，当前配置的地址给那个servlet程序使用-->
        <servlet-name>helloServlet</servlet-name>
        <!--        url-pattern 配置访问地址-->
        <!--        / 斜杠在服务器解析时候，表示地址为：http://ip:port/工程路径-->
        <!--        /hello 表示地址：http://ip:port/工程路径/hello-->
        <url-pattern>/hello</url-pattern>
    </servlet-mapping>
```

```java
@Override
    public void init(ServletConfig servletConfig) throws ServletException {
        // 获取当前Servlet程序的别名servlet-name
		System.out.println(servletConfig.getServletName());
        // 获取url值
        System.out.println(servletConfig.getInitParameter("url"));
    }
```

> 继承HttpServlet后如果要重写init方法，一定要调用父类的init方法



### ServletContext类：

1. ServletContext 是ServletConfig 的一个参数。
2. ServletContext 是一个接口，它表示 Servlet 上下文对象
3. 一个 web 工程，只有一个 ServletContext 对象实例。
4. ServletContext 对象是一个域对象。
5. ServletContext 是在 web 工程部署启动的时候创建。在 web 工程停止的时候销毁。



域对象，是可以像 Map 一样存取数据的对象，叫域对象。

这里的域指的是存取数据的操作范围，整个 web 工程。

|        |     存数据     |     取数据     |     删除数据      |
| :----: | :------------: | :------------: | :---------------: |
|  Map   |     put()      |     get()      |     remove()      |
| 域对象 | setAttribute() | getAttirbute() | removeAttribute() |





ServletContext 类的四个作用：

1. 获取 web.xml 中配置的上下文参数 context-param
2. 获取当前的工程路径，格式: /工程路径
3. 获取工程部署后在服务器硬盘上的绝对路径
4. 像 Map 一样存取数据

```xml
<!--    context-param 是上下文参数，属于整个web工程-->
    <context-param>
        <param-name>username</param-name>
        <param-value>context</param-value>
    </context-param>

    <context-param>
        <param-name>password</param-name>
        <param-value>root</param-value>
    </context-param>
```

```java
@Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) {
//        System.out.println("get");
        ServletContext context = getServletConfig().getServletContext();
        // 获取 username 对应的值
        System.out.println(context.getInitParameter("username"));
        // 获取 password 对应的值
        System.out.println(context.getInitParameter("password"));
        // 获取当前工程路径
        System.out.println(context.getContextPath());
        // 获取工程部署在服务器硬盘上的绝对路径
        System.out.println(context.getRealPath("/"));
    }
```

```java
void doGet(HttpServletRequest request, HttpServletResponse response) throws
ServletException, IOException {
// 获取 ServletContext 对象
ServletContext context = getServletContext();
System.out.println(context);
System.out.println("保存之前: Context1 获取 key1 的值是:"+ context.getAttribute("key1"));
context.setAttribute("key1", "value1");
System.out.println("Context1 中获取域数据 key1 的值是:"+ context.getAttribute("key1"));
}
```



总结：

`<context-param>`是web工程的参数

`<servlet>`是servlet程序的别名和全类名

`<init-param>`在`servlet`标签中，是servlet程序的初始化信息

`<servlet-mapping>`是配置访问地址和执行对应程序的



### HttpServletRequest 类：

##### 1. HttpServletRequest 类的作用：

每次只要有请求进入 Tomcat 服务器，Tomcat 服务器就会把请求过来的 HTTP 协议信息解析好封装到 Request 对象中。然后传递到 service 方法（doGet 和 doPost）中给我们使用。我们可以通过 HttpServletRequest 对象，获取到所有请求的信息。



##### 2. HttpServletRequest 的常用方法：

- getHeader(string name)方法：根据header参数名称获取值 ；

- getRemoteAddr()方法：发送请求的客户端主机的IP；

- getServerName()方法：服务器主机名；

- getServerPort()方法：服务器上web应用的访问端口；

- getScheme()方法：获取正确的协议，如http协议；

- getContextPath()方法：获取域名后的斜杆加工程名；

- getRequestURI()方法：将URL的域名和尾随的参数截取掉，剩下的那部分就是URI；

- getRequestURL()方法：客户请求的url，不包括参数数据；

- getMethod()方法：HTTP请求的的方法名，默认是GET，也可以指定PUT或POST；

  ```java
  public class RequestAPIServlet extends HttpServlet {
      @Override
      protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
          // 获取header参数名称对应的值
          System.out.println(req.getHeader("User-Agent"));
          // 发送请求的客户端主机的IP
          System.out.println(req.getRemoteAddr());
          // 获取服务器主机名
          System.out.println(req.getServerName());
          // 获取服务器web应用端口
          System.out.println(req.getServerPort());
          // 获取协议
          System.out.println(req.getScheme());
          // 获取域名后斜杠加工程名
          System.out.println(req.getContextPath());
          // 获取URI
          System.out.println(req.getRequestURI());
          // 获取URL
          System.out.println(req.getRequestURL());
          // 获取请求方法
          System.out.println(req.getMethod());
      }
  }
  ```

  

##### 3. HttpServletRequest  获取请求参数：

- getParameter(name)：获取请求参数值，name为html中name属性的值
- req.getParameterValues("name")：获取多个请求参数值，name为html中name属性的值



##### 4.请求转发：

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240716130803705.7sn4wcd9s1.webp)



servlet1:

```java
package com.chr.servlet;

import jakarta.servlet.RequestDispatcher;
import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

/**
 * @Author: 程浩然
 * @Create: 2024/7/15 - 19:40
 * @Description: 请求转发演示
 */
public class Servlet1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("------------------servlet1-----------------");
        // 获取请求参数
        String username = req.getParameter("username");
        System.out.println(username);
        System.out.println("run servlet1");
        // 通过域数据将数据传递到servlet2中
        req.setAttribute("key1", username);
        // 找到servlet2
        /*
          请求转发必须以斜杠开头， /斜杠表示地址为：http://ip:port/工程名/ ， 映射到IDEA代码的web目录
         */
        RequestDispatcher requestDispatcher = req.getRequestDispatcher("/servlet2");
        requestDispatcher.forward(req, resp);
    }
}
```

servlet2:

```java
package com.chr.servlet;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

/**
 * @Author: 程浩然
 * @Create: 2024/7/15 - 19:40
 * @Description: 请求转发演示
 */
public class Servlet2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("-------------------------servlet2-------------------------");
        // 查看请求参数
        String username = req.getParameter("username");
        System.out.println(username);

        // 查看servlet1想域数据的数据
        Object key1 = req.getAttribute("key1");
        System.out.println(key1);

        System.out.println("run servlet2");
    }
}
```



##### 5. base:

所有相对路径在工作时候都会参照当前浏览器地址栏地址进行跳转。

base标签可以设置当前页面中所有相对路径工作时，参照那个路径来进行跳转。在首部设置

```html
<head>
<meta charset="UTF-8">
<title>Title</title>
<!--base 标签设置页面相对路径工作时参照的地址
href 属性就是参数的地址值-->
<base href="http://localhost:8080/07_servlet/a/b/">
```



### HttpServletResponse 类：

HttpServletResponse 类和 HttpServletRequest 类一样。每次请求进来，Tomcat 服务器都会创建一个 Response 对象传递给 Servlet 程序去使用。HttpServletRequest 表示请求过来的信息，HttpServletResponse 表示所有响应的信息，我们如果需要设置返回给客户端的信息，都可以通过 HttpServletResponse 对象来进行设置.。

| 输出流 |       方法名       |             用途             |
| :----: | :----------------: | :--------------------------: |
| 字节流 | getOutputStream(); | 常用于下载（传递二进制数据） |
| 字符流 |    getWriter();    |   常用于回传字符串（常用）   |

>两个流只能使用一个，否则就会报错



##### 1. 向客户端回传字符串数据

```java
public class ResponseIOServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        req.setCharacterEncoding("UTF-8");
        resp.setHeader("Content-Type", "text/html; charset=UTF-8");
        // 得到字符流
        PrintWriter writer = resp.getWriter();
        writer.write("字符流演示");
    }
}
```

`req.setCharacterEncoding("UTF-8");`这一行设置服务器为UTF-8

`resp.setHeader("Content-Type", "text/html; charset=UTF-8");`这一行设置浏览器使用UTF-8

> `resp.setContentType("text/html; charset=UTF-8");`可以同时设置服务器和浏览器支持UTF-8（必须在获取对象流之前获取才有效）



##### 2. 请求重定向

请求重定向，是指客户端给服务器发请求，然后服务器告诉客户端说。我给你一些地址。你去新地址访问。叫请求重定向（因为之前的地址可能已经被废弃）。

客户端请求旧程序时，由于旧程序已经废弃，有义务告诉客户端，已经搬迁（响应码302）并且告知新的地址（响应头Location）。客户端收到后访问新地址。

```java
public class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("曾经使用 Response1");
        // 设置响应状态码
        resp.setStatus(302);
        // 设置响应头
        resp.setHeader("Location","http://localhost:8080/Servlet/response2");

    }
}
```

```java
public class Response2 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        PrintWriter writer = resp.getWriter();
        writer.write("response2' result");
    }
}
```

请求重定向特点：

- 浏览器地址会发生变化
- 实际是两次请求
- 不共享 Request 域中的数据
- 可以访问工程外的资源

==方法二：（推荐使用）==

```java
public class Response1 extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        System.out.println("曾经使用 Response1");
        // 方法二
        resp.sendRedirect("http://localhost:8080/Servlet/response2");
    }
}
```

