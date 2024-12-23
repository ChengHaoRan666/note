## Cookie

### 介绍：

Cookie是服务器通知客户端保存键值对的一种技术。

客户端有了Cookie后，每次请求都发送给服务器。

每个Cookie的大小不能超过4kb。



### 创建Cookie：

```java
package com.chr.cookie;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

/**
 * @Author: 程浩然
 * @Create: 2024/7/25 - 15:55
 * @Description: Cookie
 */
public class CookieServlet extends BaseServlet {
    protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        // 创建Cookie对象
        Cookie cookie = new Cookie("key1", "val1");
        // 通知客户端保存cookie
        resp.addCookie(cookie);
        resp.getWriter().write("Cookie创建成功");
    }
}
```

步骤：

1. 服务器创建cookie对象
2. 服务器通知客户端保存cookie
3. 通过响应头set-cookie通知客户端保存cookie
4. 客户端收到响应后，查看有没有这个cookie，没有就添加，有就修改



### 服务器获取cookie：

```java
protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    for (Cookie cookie : cookies) {
        resp.getWriter().write(cookie.getName() + " " + cookie.getValue() + "<br>");
    }
}
```



### 修改cookie：

##### 方案一：创建一个新的替代旧的

```java
// 修改 cookie 值方案一
protected void setCookieVal1(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    // 创建cookie
    Cookie cookie = new Cookie("key1", "newVal1");
    // 通过响应返回给客户端
    resp.addCookie(cookie);
}
```



##### 方案二：找到之前的cookie，进行修改

```java
// 修改 cookie 值方案二
protected void setCookieVal2(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    for (Cookie cookie : cookies) {
        // 找到 key1 对应的cookie
        if (cookie.getName().equals("key1")) {
            // 进行修改
            cookie.setValue("newVal1");
            // 向客户端返回响应
            resp.addCookie(cookie);
        }
    }
}
```



### cookie生命控制：

Cookie 的生命控制指的是如何管理 Cookie 什么时候被销毁

通过`setMaxAge()`方法控制

- 正数，表示在指定的秒数后过期

- 负数，表示浏览器一关，Cookie 就会被删除（默认值是-1）

- 零，表示马上删除 Cookie

```java
// 设置存活时间默认值-1
protected void defaultLife(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("defaultLife", "defaultLife");
    cookie.setMaxAge(-1); // 设置存活时间
    resp.addCookie(cookie);
}

// 设置存活时间60s
protected void life60(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("life60", "life60");
    cookie.setMaxAge(60); // 设置 Cookie 存活时间
    resp.addCookie(cookie);
    resp.getWriter().write("已经创建了一个存活60s的 Cookie");
}

// 设置存活时间0的cookie
protected void deleteNow(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    for (Cookie cookie : cookies) {
        if (cookie.getName().equals("key1")) {
            cookie.setMaxAge(0);
            resp.addCookie(cookie);
        }
    }
    resp.getWriter().write("已经删除cookie");
}
```



### cookie的Path属性：

Cookie 的 path 属性可以有效的过滤哪些 Cookie 可以发送给服务器。哪些不发。

path 属性是通过请求的地址来进行有效的过滤。

> 例子：
>
> 有两个cookie：cookieA，cookieB
>
> cookieA的path属性为：/工程路径
>
> cookieB的path属性为：/工程路径/abc
>
> 那么`http://ip:port/工程路径/a.html`访问时只有cookieA生效
>
> 那么`http://ip:port/工程路径/abc/a.html`访问时cookieA，cookieB都生效

可以通过setPath()方法设置path



### 练习：免输入登录

```java
package com.chr.cookie;

import jakarta.servlet.ServletException;
import jakarta.servlet.http.Cookie;
import jakarta.servlet.http.HttpServlet;
import jakarta.servlet.http.HttpServletRequest;
import jakarta.servlet.http.HttpServletResponse;

import java.io.IOException;

/**
 * @Author: 程浩然
 * @Create: 2024/7/25 - 18:33
 * @Description: 登录时验证
 */
public class login extends HttpServlet {
    @Override
    protected void doPost(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        if ("chr".equals(username) && "123".equals(password)) {
            Cookie usernameCookie = new Cookie("username", username);
            Cookie passwordCookie = new Cookie("password", password);
            usernameCookie.setMaxAge(60 * 60 * 24 * 7); // 设置一周内有效
            passwordCookie.setMaxAge(60 * 60 * 24 * 7); // 设置一周内有效
            resp.addCookie(usernameCookie);
            resp.addCookie(passwordCookie);
            System.out.println("登录成功");
        } else {
            System.out.println("登录失败");
        }
    }
}
```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
<base href="http://localhost:8080/Cookie_Session/">
<html>
<head>
    <title>登录</title>
</head>
<body>
<form method="post" action="login">
    用户名：<input name="username" value="${cookie.username.value}"><br>
    密码：<input name="password" type="password" value="${cookie.password.value}"><br>
    <input type="submit" value="提交">
</form>
</body>
</html>
```

```xml
<servlet>
    <servlet-name>login</servlet-name>
    <servlet-class>com.chr.cookie.login</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>login</servlet-name>
    <url-pattern>/login</url-pattern>
</servlet-mapping>
```
