## Jsp

### 什么是Jsp：

jsp 的全换是 java server pages。Java 的服务器页面。

jsp 的主要作用是代替 Servlet 程序回传 html 页面的数据。

因为 Servlet 程序回传 html 页面数据是一件非常繁锁的事情。开发成本和维护成本都极高。



### Jsp 的 page 指令：

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
```

jsp 的 page 指令可以修改 jsp 页面中一些重要的属性，或者行为。

|    属性名    |                             含义                             |
| :----------: | :----------------------------------------------------------: |
|   language   |                表示jsp翻译之后是什么语言文件                 |
| contentType  |                   表示jsp翻译后的编码类型                    |
| pageEncoding |                  表示当前jsp文件的编码类型                   |
|    import    |                     和java一样，用于导包                     |
|  autoFlush   | 设置当 out 输出流缓冲区满了之后，是否自动刷新冲级区。默认值是 true。 |
|    buffer    |              设置 out 缓冲区的大小。默认是 8kb               |
|  errorPage   |    设置当 jsp 页面运行时出错，自动跳转去的错误页面路径。     |
| isErrorPage  |     设置当前 jsp 页面是否是错误信息页面。默认是 false。      |
|   session    | 设置访问当前 jsp 页面，是否会创建 HttpSession 对象。默认是 true。 |
|   extends    |           设置 jsp 翻译出来的 java 类默认继承谁。            |



### Jsp 的常用脚本：

##### 1. 声明脚本（极少使用）

声明脚本的格式是：

```jsp
 <%! 声明 java 代码 %>
```

作用：可以给 jsp 翻译出来的 java 类定义属性和方法甚至是静态代码块。内部类等。

用法：

```jsp
<%!
    // 声明类属性
    private static Map<Integer, Integer> map;
    private static Set<Integer> set;
    static int a;

    // 声明static静态代码块
    static {
        map = new HashMap<>();
        set = new HashSet<>();
        a = 0;
    }
    
    // 声明类方法
    public int abc(){
        System.out.println("1");
        return 1;
    }
    
    // 声明内部类
    public static class a{
        private int a;
        public int b;
    }
%>
```



##### 2. 表达式脚本（常用）

表达式脚本的格式是：

```jsp
<%=表达式%>
```

表达式脚本的作用是：在 jsp 页面上输出数据。

用法：

```jsp
<%=12 %> <br>
<%=12.12 %> <br>
<%="我是字符串" %> <br>
<%=map%> <br>
<%=request.getParameter("username")%>
```



表达式脚本的特点：

1、所有的表达式脚本都会被翻译到_jspService() 方法中

2、表达式脚本都会被翻译成为 out.print()输出到页面上

3、由于表达式脚本翻译的内容都在`_jspService()` 方法中,所以`_jspService()`方法中的对象都可以直接使用。

4、==表达式脚本中的表达式不能以分号结束。==



##### 3. 代码脚本

代码脚本的格式是：

```jsp
<%java 语句%>
```



代码脚本的作用是：可以在 jsp 页面中，编写我们自己需要的功能（写的是 java 语句）。

代码脚本的特点是：

1、代码脚本翻译之后都在_jspService 方法中

2、代码脚本由于翻译到`_jspService()`方法中，所以在`_jspService()`方法中的现有对象都可以直接使用。

3、还可以由多个代码脚本块组合完成一个完整的 java 语句。

4、代码脚本还可以和表达式脚本一起组合使用，在 jsp 页面上输出数据。



### Jsp 的三种注释：

```jsp
<!-- 这是 html 注释 -->

<%
// 单行 java 注释
/* 多行 java 注释 */
%>

<%-- 这是 jsp 注释 --%>
```



### Jsp 的九大内置对象：

|   对象名    |        含义        |
| :---------: | :----------------: |
|   request   |      请求对象      |
|  response   |      响应对象      |
| pageContext |  jsp的上下文对象   |
|   session   |      会话对象      |
| application | ServletContext对象 |
|   config    | ServletConfig对象  |
|     out     |   jsp输出流对象    |
|    page     | 指向当前jsp的对象  |
|  exception  |      异常对象      |



### Jsp 的四大域对象：

|   变量名    |         类名         |                           有效范围                           |
| :---------: | :------------------: | :----------------------------------------------------------: |
| pageContext |  PageContextImpI类   |                     当前jsp页面范围有效                      |
|   request   | HttpServletRequest类 |                        一次请求内有效                        |
|   session   |    HttpSession类     | 一次会话范围内有效<br />（打开浏览器访问浏览器，直到关闭浏览器） |
| application |   ServletContext类   |                    整个web工程范围内有效                     |

域对象是可以向Map一样存取数据的对象。四个域对象功能一样。不同的是他们对数据的存取范围。

虽然四个域对象可以存取数据。但在使用上他们是有优先顺序的。从优先开始顺序如下：

pageContext ==>>>  request  ==>>>  session  ==>>>  application

```jsp
<%--存数据--%>
<%
    pageContext.setAttribute("pageContext_key", "pageContext_val");
    request.setAttribute("request_key", "request_val");
    session.setAttribute("session_key", "session_val");
    application.setAttribute("application_key", "application_val");
%>

<br>

<%--取数据--%>
pageContext域中值为：<%=pageContext.getAttribute("pageContext_key")%><br>
request域中值为：<%=request.getAttribute("request_key")%><br>
session域中值为：<%=session.getAttribute("session_key")%><br>
application域中值为：<%=application.getAttribute("application_key")%><br>
```





### Jsp 中的 out 输出和 response.getWriter 输出的区别：

> 在 jsp 页面中，可以统一使用 out.print()来进行输出



### Jsp 的常用标签：

##### 1. 静态包含

```jsp
<%@ include file="/html/footer.jsp"%>
```


地址中第一个斜杠 / 表示为 `http://ip:port/工程路径/映射到代码的 web 目录 `



静态包含的特点：

1. 静态包含不会翻译被包含的 jsp 页面。
2. 静态包含其实是把被包含的 jsp 页面的代码拷贝到包含的位置执行输出。



##### 2. 动态包含

```jsp
<jsp:include page="/html/footer.jsp">
    <jsp:param name="username" value="bbj"/>
    <jsp:param name="password" value="root"/>
</jsp:include>
```

page 属性是指定你要包含的 jsp 页面的路径。动态包含也可以像静态包含一样。把被包含的内容执行输出到包含位置。

动态包含的特点：

1. 动态包含会把包含的 jsp 页面也翻译成为 java 代码。
2. 动态包含，还可以传递参数。通过request.getParameter("username")获取。



##### 3. 转发

```jsp
<jsp:forward page="/html/footer.jsp"></jsp:forward>
```

就是请求转发，访问网址的时候会跳到另一个网址。



### Listener监听器

##### 什么是Listener监听器：

1. Listener 监听器它是 JavaWeb 的三大组件之一。JavaWeb 的三大组件分别是：Servlet 程序、Filter 过滤器、Listener 监听器。
2. Listener 它是 JavaEE 的规范，就是接口。
3. 监听器的作用是，监听某种事物的变化。然后通过回调函数，反馈给客户（程序）去做一些相应的处理。



##### ServletContextListener监听器：

1. ServletContextListener 它可以监听 ServletContext 对象的创建和销毁。

2. ServletContext 对象在 web 工程启动的时候创建，在 web 工程停止的时候销毁。

3. 监听到创建和销毁之后都会分别调用 ServletContextListener 监听器的方法反馈。

```java
package jakarta.servlet;

import java.util.EventListener;
// ServletContextListener 的接口，里面有两个方法
public interface ServletContextListener extends EventListener {
    // 这个方法在 ServletContext 对象创建之后马上调用，做初始化
    default void contextInitialized(ServletContextEvent sce) {
    }
    
    // 这个方法在 ServletContext 对象销毁之后调用
    default void contextDestroyed(ServletContextEvent sce) {
    }
}
```



使用方法：

1. 创建一个类实现 ServletContextListener 接口
2. 实现两个方法
3. 在web.xml中配置监听器

```java
public class MyServletContextListener implements ServletContextListener {
    // 创建时调用
    @Override
    public void contextInitialized(ServletContextEvent sce) {
        System.out.println("ServletContext对象创建");
    }

    // 删除时调用
    @Override
    public void contextDestroyed(ServletContextEvent sce) {
        System.out.println("ServletContext对象销毁");
    }
}
```

```xml
<listener>
    <listener-class>MyServletContextListener</listener-class>
</listener>
```
