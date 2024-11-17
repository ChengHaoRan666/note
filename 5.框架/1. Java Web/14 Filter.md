## Filter

### Filter的作用：

1. Filter 过滤器它是 JavaWeb 的三大组件之一。
   三大组件分别是：Servlet 程序、Listener 监听器、Filter 过滤器
2. Filter 过滤器它是 JavaEE 的规范。也就是接口。
3. Filter 过滤器它的作用是：<font color="red">拦截请求</font>，过滤响应。

> 用户登录之后都会把用户登录的信息保存到 Session 域中。所以要检查用户是否登录，可以判断 Session 中否包含有用户登录的信息即可

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240726195952452.8dwsin7q2w.webp)

### 实现filter过滤：

1. 编写一个类去实现 Filter 接口

2. 实现过滤方法 doFilter()

3. 到 web.xml 中去配置 Filter 的拦截路径

```java
package com.chr;

import jakarta.servlet.*;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpSession;

import java.io.IOException;

/**
 * @Author: 程浩然
 * @Create: 2024/7/26 - 19:53
 * @Description: 实现 Filter 过滤器
 */
public class AdminFilter implements Filter {

    @Override
    public void init(FilterConfig filterConfig) throws ServletException {
        Filter.super.init(filterConfig);
    }

    /**
     * doFilter 方法，专门用于拦截请求。可以做权限检查
     */
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        // 先将 servletRequest 转为HttpServletRequest，然后获取session库
        HttpSession session = ((HttpServletRequest) servletRequest).getSession();
        // 获取 session 域中 user 值
        Object user = session.getAttribute("user");
        if (user == null) {
       servletRequest.getRequestDispatcher("/login.jsp").forward(servletRequest, servletResponse);
            return;
        } else {
            // 让程序继续访问用户的目标资源（很重要）
            filterChain.doFilter(servletRequest, servletResponse);
        }
    }

    @Override
    public void destroy() {
        Filter.super.destroy();
    }
}
```

在xml上的配置：

```xml
<!--给filter起一个别名-->
<filter>
    <filter-name>AdminFilter</filter-name>
    <!--全类名-->
    <filter-class>com.chr.AdminFilter</filter-class>
</filter>
<!--配置filter过滤器的拦截路径-->
<filter-mapping>
    <filter-name>AdminFilter</filter-name>
    <url-pattern>/admin/*</url-pattern>
</filter-mapping>
```

servlet程序：

```java
public class LoginServlet extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        resp.setContentType("text/html; charset=UTF-8");
        String username = req.getParameter("username");
        String password = req.getParameter("password");

        if (username.equals("chr") && password.equals("123")) {
            // 将user数据存入session域中
            req.getSession().setAttribute("user", username);
            resp.getWriter().write("登录成功");
        } else {
            req.getRequestDispatcher("/login.jsp").forward(req, resp);
        }
    }
}
```



### Filter的生命周期：

Filter 的生命周期包含几个方法：

1. ==构造器方法==

2. ==init 初始化方法==

3. ==doFilter 过滤方法==

4. ==destroy 销毁==



第 1，2 步，在 web 工程启动的时候执行（Filter 已经创建）

第 3 步，每次拦截到请求，就会执行

第 4 步，停止 web 工程的时候，就会执行（停止 web 工程，也会销毁 Filter 过滤器）



### FilterConfig 类:

FilterConfig 类是 Filter 过滤器的配置文件类。

Tomcat 每次创建 Filter 的时候，也会同时创建一个 FilterConfig 类，这里包含了 Filter 配置文件的配置信息。

FilterConfig 类的作用是获取 filter 过滤器的配置内容：

1. 获取 Filter 的名称 filter-name 的内容

2. 获取在 Filter 中配置的 init-param 初始化参数

3. 获取 ServletContext 对象



filter的配置信息：

```xml
<!--filter初始化配置-->
<init-param>
    <param-name>username</param-name>
    <param-value>root</param-value>
</init-param>
```

在`<filter></filter>`标签中。

```java
// 1、获取 Filter 的名称 filter-name 的内容
System.out.println("filter-name 的值是：" + filterConfig.getFilterName());
// 2、获取在 web.xml 中配置的 init-param 初始化参数
System.out.println("初始化参数 username 的值是：" + filterConfig.getInitParameter("username"));
// 3、获取 ServletContext 对象
System.out.println(filterConfig.getServletContext());
```



### Filter 的拦截路径：

##### 1. 精确匹配

`<url-pattern>/target.jsp</url-pattern>`

以上配置的路径，表示请求地址必须为：`http://ip:port/工程路径/target.jsp` 



##### 2. 目录匹配

`<url-pattern>/admin/*</url-pattern>`

以上配置的路径，表示请求地址必须为：`http://ip:port/工程路径/admin/* `



##### 3. 后缀名匹配

`<url-pattern>.html</url-pattern>`

以上配置的路径，表示请求地址必须以.html 结尾才会拦截到

`<url-pattern>.do</url-pattern>`

以上配置的路径，表示请求地址必须以.do 结尾才会拦截到

`<url-pattern>.action</url-pattern>`

以上配置的路径，表示请求地址必须以.action 结尾才会拦截到

> Filter 过滤器它只关心请求的地址是否匹配，不关心请求的资源是否存在

