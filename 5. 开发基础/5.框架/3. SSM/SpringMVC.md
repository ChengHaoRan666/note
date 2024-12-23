## 一. SpringMVC 简介

### 1. 什么是 MVC

MVC是一种软件架构的思想，将软件按照模型、视图、控制器来划分

M：Model，模型层，指工程中的JavaBean，作用是处理数据

>JavaBean分为两类：
>
>- 一类称为实体类Bean：专门存储业务数据的，如 Student、User 等
>- 一类称为业务处理 Bean：指 Service 或 Dao 对象，专门用于处理业务逻辑和数据访问。

V：View，视图层，指工程中的html或jsp等页面，作用是与用户进行交互，展示数据

C：Controller，控制层，指工程中的servlet，作用是接收请求和响应浏览器

MVC的工作流程：
用户通过视图层发送请求到服务器，在服务器中请求被控制层接收，控制层调用相应的模型层处理请求，处理完毕将结果返回到控制层，控制层再根据请求处理的结果找到相应的视图，渲染数据后最终响应给浏览器

> 注：三层架构分为表述层（或表示层）、业务逻辑层、数据访问层，表述层表示前台页面和后台servlet



### 2. SpringMVC 的特点

- **Spring 家族原生产品**，与 IOC 容器等基础设施无缝对接
- **基于原生的Servlet**，通过了功能强大的**前端控制器DispatcherServlet**，对请求和响应进行统一处理
- 表述层各细分领域需要解决的问题**全方位覆盖**，提供**全面解决方案**
- **代码清新简洁**，大幅度提升开发效率
- 内部组件化程度高，可插拔式组件**即插即用**，想要什么功能配置相应组件即可
- **性能卓著**，尤其适合现代大型、超大型互联网项目要求



## 二. HelloWord

### 1. 创建 Maven 文件，设置 web 框架

### 2. 导入 jar 包

```xml
		<!-- SpringMVC -->
        <dependency>
            <groupId>org.springframework</groupId>
            <artifactId>spring-webmvc</artifactId>
            <version>6.1.6</version>
        </dependency>

        <!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.2.3</version>
        </dependency>

        <!-- ServletAPI -->
        <dependency>
            <groupId>jakarta.servlet</groupId>
            <artifactId>jakarta.servlet-api</artifactId>
            <version>6.0.0</version>
            <scope>provided</scope>
        </dependency>

        <!-- Spring6和Thymeleaf整合包 -->
        <dependency>
            <groupId>org.thymeleaf</groupId>
            <artifactId>thymeleaf-spring6</artifactId>
            <version>3.1.2.RELEASE</version>
        </dependency>

		<!-- 用于直接生成get，set，tostring方法的-->
        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>compile</scope>
        </dependency>
```

### 3. 配置 **前端控制器 DispatcherServlet**

> 前端控制器实际上是一个servlet程序，用于处理所以请求，需要对它在web.xml中注册。在注册时配置了SpringMVC的配置文件的位置和名称，以及前端控制器的初始化时间提前到服务器启动时

```xml
<servlet>
    <servlet-name>SpringMVC</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
</servlet>

<!--
		设置springMVC的核心控制器所能处理的请求的请求路径
		/所匹配的请求可以是/login或.html或.js或.css方式的请求路径
		但是/不能匹配.jsp请求路径的请求
 -->
<servlet-mapping>
    <servlet-name>SpringMVC</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

### 4. 配置 SpringMVC 的配置文件的位置和名称

1. 情况一：默认配置方式
   SpringMVC 的配置文件位于 WEB-INF 下，默认名称为`<servlet-name>`(的值) -servlet.xml。

   

2. 情况二：拓展配置方式
   通过`<init-param>`标签设置SpringMVC配置文件的位置和名称，通过`<load-on-startup>`标签设置SpringMVC前端控制器DispatcherServlet 的初始化时间。

   ```xml
   <servlet>
       <servlet-name>SpringMVC</servlet-name>
       <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
       <!--配置SpringMVC配置文件的位置和名称-->
       <init-param>
           <!--配置SpringMVC配置文件的位置和名称-->
           <param-name>contextConfigLocation</param-name>
           <param-value>classpath:springMVC.xml</param-value>
       </init-param>
       <!--将前端控制器 DispatcherServlet 初始化时间提前到服务器启动时-->
       <load-on-startup>1</load-on-startup>
   </servlet>
   <!--
       设置springMVC的核心控制器所能处理的请求的请求路径
       /所匹配的请求可以是/login或.html或.js或.css方式的请求路径
       但是/不能匹配.jsp请求路径的请求
   -->
   <servlet-mapping>
       <servlet-name>SpringMVC</servlet-name>
       <url-pattern>/</url-pattern>
   </servlet-mapping>
   ```



### 5.  创建请求控制器

前端控制器对于浏览器的请求进行了统一的处理，但是具体的请求有不同的处理过程，一次需要创建处理具体请求的类，即请求控制器。

请求控制器中每一个处理请求的方法称为控制器方法。

应为SpringMVC的控制器由一个POJO(普通的java类)担任，因此需要通过==@Controller==注解将其标识为一个控制层组件，交给Spring的IOC容器管理，才能识别出控制器的存在。

注意：使用注解要开启组件扫描功能

```java
@Controller
public class HelloController {

}
```

```xml
<context:component-scan base-package="com.chr.SpringMVC.controller"/>
```

开启视图解析器

```xml
<!-- 配置Thymeleaf视图解析器 -->
    <bean id="viewResolver"
          class="org.thymeleaf.spring6.view.ThymeleafViewResolver">
        <property name="order" value="1"/>
        <property name="characterEncoding" value="UTF-8"/>
        <property name="templateEngine">
            <bean class="org.thymeleaf.spring6.SpringTemplateEngine">
                <property name="templateResolver">
                    <bean class="org.thymeleaf.spring6.templateresolver.SpringResourceTemplateResolver">
                        <!-- 视图前缀 -->
                        <property name="prefix" value="/WEB-INF/templates/"/>
                        <!-- 视图后缀 -->
                        <property name="suffix" value=".html"/>
                        <property name="templateMode" value="HTML"/>
                        <property name="characterEncoding" value="UTF-8"/>
                    </bean>
                </property>
            </bean>
        </property>
    </bean>
```



编写首页的请求映射：

> @RequestMapping(value = "/") 注解用于进行请求映射

```java
@Controller
public class HelloController {
    @RequestMapping(value = "/")
    public String index() {
        // 返回视图名称
        return "index";
    }
}
```

### 6. 测试

![](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image-20240813153712344.6t71j6aimn.webp)

添加一个跳转功能

```html
<!--通过 thymeleaf 来将相对路径转换为绝对路径-->
<a th:href="@{/target}">方位目标页面 target.html</a>
```

```java
@RequestMapping(value = "/target")
public String toTarget() {
    return "target";
}
```

![](https://github.com/myself54188/picx-images-hosting/raw/master/image-20240813154323944.45lufddxs.webp)

### 7. 总结

1. 浏览器发送请求。
2. 若请求地址符合前端控制器的url-pattern，该请求就会被前端控制器DispatcherServlet 处理。
3. 前端控制器会读取 SpringMVC 的核心配置文件，通过扫描组件找到控制器，将请求地址和控制器中==@RequestMapping==注解的value属性值进行匹配。
4. 若匹配成功，该注解所标识的控制器方法就是处理请求的方法。处理请求的方法需要返回一个字符串类型的视图名称，该视图名称会被视图解析器解析，加上前缀和后缀组成视图的路径。
5. 之后通过Thymeleaf对视图进行渲染，最终<font color="red">转发</font>到视图所对应页面。



## 三. @RequestMapping 注解

### 1.@RequestMapping 注解的功能

==@RequestMapping== 注解的作用就是将请求和处理请求的控制器方法关联起来，建立映射关系。

SpringMVC 接收到指定的请求，就会来找到在映射关系中对应的控制器方法来处理这个请求。

> 同一个初始信息的类中的控制器所对应的匹配路径是唯一的（即value的值唯一，不能重复）



### 2. @RequestMapping 注解的位置

@RequestMapping标识一个类：设置映射请求的请求路径的初始信息

@RequestMapping标识一个方法：设置映射请求请求路径的具体信息

```java
// 这时候需要访问 http://localhost:8080/SpringMVC_demo2/hello/testRequestMapping
@Controller
@RequestMapping(value = "/hello")
public class RequestMappingController {
    @RequestMapping(value = "/testRequestMapping")
    public String success() {
        return "success";
    }
}
```

> 通常会将同一个模块的功能放在一个类中，在类上进行标识，以便区分。
>
> 比如用户和管理员会创建两个类，分别写两套控制器，在类上会写不同的 RequestMapping，但在两个类中，RequestMapping 值可能相同。



### 3. @RequestMapping 注解的value属性

value属性通过请求的<font color="red">请求地址</font>匹配请求映射

value属性是一个**字符串类型**的数组，表示该请求映射能够匹配多个请求地址所对应的请求，只要满足一个请求地址即可

==@RequestMapping== 注解的 value 属性必须设置

```java
@RequestMapping(value = {"/testRequestMapping","/test"})
public String success() {
    return "success";
}
```

```html
<a th:href="@{/testRequestMapping}">超链接1</a><br>
<a th:href="@{/test}">超链接2</a><br>
```



### 4. @RequestMapping 注解的 method 属性

method 属性通过请求的<font color="red">请求方式</font>（get或post）匹配请求映射

method属性是一个RequestMethod类型的 **数组**，表示该请求映射能够匹配多种请求方式的请求，只要满足一个请求方式即可

若当前请求的请求地址满足请求映射的value属性，但是请求方式不满足method属性，则浏览器报错405：Request method 'POST' not supported

> ==@RequestMapping== 默认是匹配 get 和 post 都满足

```java
@RequestMapping(value = {"/testRequestMapping", "/test"}, method = {RequestMethod.GET})
public String success() {
    return "success";
}
```

```html
<form th:action="@{/test}" method="post">
    <input type="submit" value="post 提交">
</form>
<br>
<form th:action="@{/test}" method="get">
    <input type="submit" value="get 提交">
</form>
```

> 注：
>
> 1、对于处理指定请求方式的控制器方法，SpringMVC中提供了@RequestMapping的派生注解
>
> 处理get请求的映射-->@GetMapping
>
> 处理post请求的映射-->@PostMapping
>
> 处理put请求的映射-->@PutMapping
>
> 处理delete请求的映射-->@DeleteMapping
>
> 2、常用的请求方式有get，post，put，delete
>
> 但是目前浏览器只支持get和post，若在form表单提交时，为method设置了其他请求方式的字符串（put或delete），则按照默认的请求方式get处理
>
> 若要发送put和delete请求，则需要通过spring提供的过滤器HiddenHttpMethodFilter，在RESTful 部分会讲到



### 5. @RequestMapping 注解的 params 属性

params 属性是通过请求的<font color="red">请求参数</font>匹配请求映射，必须同时满足所有 param 条件

params 属性是一个字符串类型的数组，可以通过四种表达式设置参数和请求映射的匹配关系：

- `"param"`：要求请求映射所匹配的请求必须携带 param 请求参数
- `"!param"`：要求请求映射所匹配的请求必须不能携带 param 请求参数
- `"param=value"`：要求请求映射所匹配的请求必须携带 param 请求参数且 param=value
- `"param!=value"`：要求请求映射所匹配的请求必须携带 param 请求参数但是param!=value

```java
 @RequestMapping(value = "/test",
            params = {"username",
//                    "!username",
//                    "username=1",
//                    "username!=1"
            }
    )
    public String test() {
        System.out.println("params");
        return "success";
    }
```

```html
<form th:action="@{/test}" method="get">
    <input type="text" name="username">
    <input type="submit" value="提交">
</form>
```

> 注：
>
> 若当前请求满足@RequestMapping注解的value和method属性，但是不满足params属性，此时页面回报错400：Parameter conditions "username, password!=123456" not met for actual request parameters: username={admin}, password={123456}



### 6. @RequestMapping 注解的 headers 属性

headers 属性通过请求的<font color="red">请求头信息</font>匹配请求映射

headers属性是一个字符串数组，可以通过四种表达式设置请求头信息和请求映射的匹配关系

- `"header"`：要求请求映射所匹配的请求必须携带header请求头信息

- `"!header"`：要求请求映射所匹配的请求必须不能携带header请求头信息

- `"header=value"`：要求请求映射所匹配的请求必须携带header请求头信息且header=value

- `"header!=val"`：要求请求映射所匹配的请求必须携带header请求头信息且header != val

```java
 @RequestMapping(value = "/test",
            headers = {"Host",
//                    "!Host",
//                    "Host=localhoot:8080",
//                    "Host!=localhoot:8080"
            }
    )
    public String test() {
        System.out.println("headers");
        return "success";
    }
```

```html
<form th:action="@{/test}" method="get">
    <input type="text" name="username">
    <input type="submit" value="提交">
</form>
```



>若当前请求满足@RequestMapping注解的value和method属性，但是不满足headers属性，此时页面显示404错误，即资源未找到



### 7. SpringMVC 支持 ant 风格(模糊匹配)的路径

-  `？`：表示任意的单个字符

- `*`：表示任意的0个或多个字符



### 8. SpringMVC 支持路径中的占位符

原始方式：/deleteUser?id=1

rest方式：/deleteUser/1(不采用 key = val 的形式)

SpringMVC路径中的占位符常用于RESTful风格中，当请求路径中将某些数据通过路径的方式传输到服务器中，就可以在相应的 @RequestMapping 注解的value属性中通过占位符 {xxx} 表示传输的数据，在通过==@PathVariable==注解，将占位符所表示的数据赋值给控制器方法的形参

```html
<a th:href="@{/testRest/1/admin}">测试路径中的占位符-->/testRest</a><br>
```

```java
@RequestMapping("/testRest/{id}/{username}")
public String testRest(@PathVariable("id") String id, @PathVariable("username") String username){
    System.out.println("id:"+id+",username:"+username);
    return "success";
}
//最终输出的内容为-->id:1,username:admin
```



## 四. SpringMVC获取请求参数

###  1. 通过 ServletAPI 获取

将 HttpServletRequest 作为控制器方法的形参，此时 HttpServletRequest 类型的参数表示封装了当前请求的请求报文的对象

```html
<a th:href="@{/paramAPI?username='ad'&password=123}">通过req获取请求参数</a><br>
```

```java
@RequestMapping(value = "/paramAPI")
    public String paramAPI(HttpServletRequest req) {
        String username = req.getParameter("username");
        String password = req.getParameter("password");
        System.out.println("通过API获取：" + username + " " + password);
        return "Param";
    }
```



### 2. 通过控制器方法的形参获取请求参数

在控制器方法的形参位置，<font color="red">设置和请求参数同名的形参</font>，当浏览器发送请求，匹配到请求映射时，在 DispatcherServlet 中就会将<font color="red">请求参数赋值给相应的形参</font>

```html
<a th:href="@{/testParam?username='ad'&password=123}">通过控制器的形参获取请求参数</a>
```

```java
@RequestMapping(value = "/testParam")
    public String testParam(Object username, Object password) {
        System.out.println("通过控制器获取：" + username + " " + password);
        return "Param";
    }
```

> 注：
>
> 若请求所传输的请求参数中有多个同名的请求参数，此时可以在控制器方法的形参中设置字符串数组或者字符串类型的形参接收此请求参数
>
> 若使用字符串数组类型的形参，此参数的数组中包含了每一个数据
>
> 若使用字符串类型的形参，此参数的值为每个数据中间使用逗号拼接的结果



### 3. @RequestParam

@RequestParam是将<font color="red">请求参数和控制器方法的形参</font>创建映射关系

@RequestParam注解一共有三个属性：

- value：指定为形参赋值的请求参数的参数名

- required：设置是否必须传输此请求参数，默认值为true

> 若设置为true时，则当前请求必须传输value所指定的请求参数，若没有传输该请求参数，且没有设置defaultValue属性，则页面报错400：Required String parameter 'xxx' is not present；
>
> 若设置为false，则当前请求不是必须传输value所指定的请求参数，若没有传输，则注解所标识的形参的值为null

- defaultValue：不管required属性值为true或false，当value所指定的请求参数没有传输或传输的值为""时，则使用默认值为形参赋值

```html
<a th:href="@{/RequestParam?username='ad'&password=123}">通过 @RequestParam 获取请求参数</a><br>
```

```java
@RequestMapping(value = "/RequestParam")
public String RequestParam(
        @RequestParam("username") String username,
        @RequestParam("password") String password
) {
    System.out.println("通过 RequestParam 获取：" + username + " " + password);
    return "Param";
}
```



### 4. @RequestHeader 获取 Header 的值

@RequestHeader是将<font color="red">请求头信息和控制器方法的形参</font>创建映射关系

@RequestHeader注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```html
<a th:href="@{/RequestHeader}">通过 @RequestHeader 获取请求头信息</a><br>
```

```java
@RequestMapping(value = "/RequestHeader")
public String RequestHeader(@RequestHeader("Host") String host) {
    System.out.println("通过 RequestHeader 获取 Host 为：" + host);
    return "Param";
}
```



### 5. @CookieValue 获取 Cookie 的值

@CookieValue是将<font color="red">cookie数据和控制器方法的形参</font>创建映射关系

@CookieValue注解一共有三个属性：value、required、defaultValue，用法同@RequestParam

```java
@RequestMapping(value = "/CookieValue")
public String CookieValue(@CookieValue("值") String cookie) {
    System.out.println(cookie);
    return "Param";
}
```



### 6. 通过 POJO 获取请求参数

可以在控制器方法的形参位置设置一个实体类类型的形参，此时若浏览器传输的请求参数的参数名和实体类中的属性名一致，那么请求参数就会为此属性赋值

```html
<form th:action="@{/pojoTest}" method="post" name="form">
    用户名：<input type="text" name="username"><br>
    密码：<input type="text" name="password"><br>
    性别：<input type="text" name="sex"><br>
    年龄：<input type="" name="age"><br>
    邮箱：<input type="text" name="email"><br>
    <input type="submit" name="提交">
</form>
```

```java
@RequestMapping(value = "/pojoTest")
    public String pojoTest(User user) {
        System.out.println(user);
        return "Param";
    }
```

```java
package com.chr.SpringMVC.bean;

import lombok.Data;


@Data
// 使用 lombok jar 包，仅需加上@Data注解即可实现生成get，set，构造，toString 方法
public class User {
    String username;
    String password;
    String sex;
    BigDecimal age;
    String email;
}
```



### 7. 解决控制台输出乱码问题

解决获取请求参数的乱码问题，可以使用SpringMVC提供的编码过滤器 CharacterEncodingFilter，但是必须在web.xml中进行注册

```xml
<!--配置springMVC的编码过滤器-->
<filter>
    <filter-name>CharacterEncodingFilter</filter-name>
    <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
    <init-param>
        <param-name>encoding</param-name>
        <param-value>UTF-8</param-value>
    </init-param>
    <init-param>
        <param-name>forceResponseEncoding</param-name>
        <param-value>true</param-value>
    </init-param>
</filter>
<filter-mapping>
    <filter-name>CharacterEncodingFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

> 注：
>
> SpringMVC中处理编码的过滤器一定要配置到其他过滤器之前，否则无效



## 五. 域对象共享数据

### 0. 预对象回顾

- Request 域：从 HTTP 请求发起，到服务器吹了结束并返回响应的整个过程，在这个过程中，如果使用了 forward 方式（请求转发）跳转到其他页面，这些页面都可以使用在 Request 域中设置的变量。但是一旦页面刷新，变量就会重新计算。
- session 域：有效范围是当前会话，即从用户打开浏览器开始，到关闭浏览器的整个工程，这个过程可能包含多个请求响应，只要用户不关闭浏览器，服务器就可以识别这些请求来自用一个会话。浏览器关闭，session 域中数据清空。
- application 域：作用范围是这个 web 应用程序，当服务器重启，application 域中数据清空。 



### 1. 使用 ServletAPI 向 Request 域对象共享数据

```java
@RequestMapping(value = "/servletAPITest")
public String servletAPITest(HttpServletRequest request) {
    request.setAttribute("testScope", "servletAPITest");
    return "success";
}
```

```html
<!--index.html-->
<body>
<h1>index</h1>
<iframe th:src="@{/success.html}" width="800" height="300" name="iframe"></iframe><br>
<a th:href="@{/servletAPITest}" target="iframe">使用 servletAPI 向 Request 域中存储数据</a><br>
</body>
```

```html
<!--success.html-->
<body>
<h1>跳转成功</h1>
<p th:text="${testScope}"></p><br>
</body>
```

效果：

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.839ys4lgey.webp)



### 2. 使用 ModelAndView 向 Request 域对象共享数据

```java
@RequestMapping(value = "/ModelAndViewTest")
public ModelAndView ModelAndViewTest() {
    ModelAndView mav = new ModelAndView();
    // 处理模型数据
    mav.addObject("testScope", "ModelAndViewTest");
    // 设置视图名称
    mav.setViewName("success");
    return mav;
}
```

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.26lel45rg0.webp)



### 3. 使用 Model 向 Request 域对象共享数据

```java
@RequestMapping(value = "/modelTest")
public String modelTest(Model model) {
    model.addAttribute("testScope", "modelTest");
    return "success";
}
```

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.b8tshye1c.webp)





### 4. 使用 map 向 Request 域对象共享数据

```java
@RequestMapping("/mapTest")
public String mapTest(Map<String, Object> map){
    map.put("testScope", "mapTest");
    return "success";
}
```

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.5fkihs1l0s.webp)



### 5. 使用 ModelMap 向 Request 域对象共享数据

```java
@RequestMapping("/modelMapTest")
public String modelMapTest(ModelMap modelMap){
    modelMap.addAttribute("testScope", "modelMapTest");
    return "success";
}
```

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.58hamchmo9.webp)



### 6. Model，ModelMap，Map 的关系

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.2a50iuiwjo.webp)



### 7. 向 session 域共享数据

```java
@RequestMapping(value = "sessionTest")
public String sessionTest(HttpSession session) {
    session.setAttribute("sessionScope", "sessionTest");
    return "success";
}
```

```html
<p th:text="${session.sessionScope}"></p><br>
```

>  使用 session 域中的数据必须采用 session.key 的格式获取 Value 的值

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.7egp851qs9.webp)



### 8. 向 application 域共享数据

```java
@RequestMapping(value = "/applicationTest")
public String applicationTest(HttpSession session) {
    ServletContext application = session.getServletContext();
    application.setAttribute("applicationScope", "applicationTest");
    return "success";
}
```

```html
<p th:text="${application.applicationScope}"></p>
```

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.3uurik7ecn.webp)



## 六、SpringMVC的视图

- SpringMVC中的视图是View接口，视图的作用渲染数据，将模型Model中的数据展示给用户

- SpringMVC视图的种类很多，默认有<font color="red">转发视图</font>和<font color="red">重定向视图</font>

- 当工程引入 jstl 的依赖，转发视图会自动转换为 JstlView

- 若使用的视图技术为Thymeleaf，在SpringMVC的配置文件中配置了Thymeleaf的视图解析器，由此视图解析器解析之后所得到的是 ThymeleafView

### 1. ThymeleafView

当控制器方法中所设置的视图名称<font color="red">没有任何前缀</font>时，此时的视图名称会被SpringMVC配置文件中所配置的视图解析器解析，视图名称拼接视图前缀和视图后缀所得到的最终路径，会通过**转发**的方式实现跳转

```java
@RequestMapping("/testHello")
public String testHello(){
    return "hello";
}
```



### 2. 转发视图

SpringMVC中默认的转发视图是InternalResourceView

SpringMVC中创建转发视图的情况：

当控制器方法中所设置的<font color="red">视图名称以"forward:"为前缀</font>时，创建InternalResourceView视图，此时的视图名称不会被 SpringMVC 配置文件中所配置的视图解析器解析，而是会将前缀"forward:"去掉，剩余部分作为最终路径通过转发的方式实现跳转

```java
@RequestMapping("/viewForward")
public String viewForward() {
    return "forward:/view";
}
```



### 3. 重定向视图

SpringMVC中默认的重定向视图是RedirectView

当控制器方法中所设置的<font color="red">视图名称以"redirect:"为前缀</font>时，创建RedirectView视图，此时的视图名称不会被SpringMVC配置文件中所配置的视图解析器解析，而是会将前缀"redirect:"去掉，剩余部分作为最终路径通过重定向的方式实现跳转

```java
@RequestMapping("/viewRedirect")
public String viewRedirect() {
    return "redirect:/view";
}
```



### 4. 视图控制器view-controller

当控制器方法中，仅仅用来实现页面跳转，即只需要设置视图名称时，可以将处理器方法使用view-controller标签进行表示

```java
@RequestMapping("/")
public String view() {
    return "view";
}
```

上面可以写成下面这种形式：

```xml
<!--
	path：设置处理的请求地址
	view-name：设置请求地址所对应的视图名称
-->
<mvc:view-controller path="/" view-name="view"/>
    <!--开启MVC的注解驱动-->
    <mvc:annotation-driven/>
```

> 注：
>
> 当SpringMVC中设置任何一个view-controller时，其他控制器中的请求映射将全部失效，此时需要在SpringMVC的核心配置文件中设置开启mvc注解驱动的标签：
>
> <mvc:annotation-driven />



## 七、RESTful

### 1. RESTful简介

REST：**Re**presentational **S**tate **T**ransfer，表现层资源状态转移。



### 2. RESTful的实现

具体说，就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。

它们分别对应四种基本操作：

- GET 用来获取资源
- POST 用来新建资源
- PUT 用来更新资源
- DELETE 用来删除资源。

REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

| 操作     | 传统方式         | REST风格                |
| -------- | ---------------- | ----------------------- |
| 查询操作 | getUserById?id=1 | user/1-->get请求方式    |
| 保存操作 | saveUser         | user-->post请求方式     |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式 |
| 更新操作 | updateUser       | user-->put请求方式      |



### 3. HiddenHttpMethodFilter

>  **HiddenHttpMethodFilter** 过滤器可以帮助我们将 POST 请求转换为 DELETE 或 PUT 请求

```xml
<!--注册 HiddenHttpMethodFilter 过滤器-->
<filter>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
</filter>
<filter-mapping>
    <filter-name>HiddenHttpMethodFilter</filter-name>
    <url-pattern>/*</url-pattern>
</filter-mapping>
```

转换为 DELETE 或 PUT 的步骤：

1. 在 web.xml 中注册过滤器
2. 在传输的表单中加上一个隐藏域，name 为 `_method` ，value 为 `put` 或 `delete`



### 4. 案例

```txt
使用 RESTful 风格来进行用户信息的怎删改查
/user      POST    添加用户信息
/user/1    DELETE  删除用户信息
/user      PUT     修改用户信息
/user      GET     查询所有用户信息
/user/1    GET     查询用户信息
```

增：

```html
<form th:action="@{/user}" method="post">
    用户名： <input type="text" name="username"><br>
    密 码：<input type="password" name="password"><br>
    <input type="submit" value="添加">
</form>
```

```java
@RequestMapping(value = "/user", method = RequestMethod.POST)
public String addUser(
        @RequestParam("username") String username,
        @RequestParam("password") String password
) {
    System.out.println("添加用户信息：" + username + " " + password);
    return "success";
}
```



删：

```html
<form id="deleteForm" th:action="@{/user/1}" method="post" style="display: none;">
    <input type="hidden" name="_method" value="delete">
    <button type="submit">Delete</button>
</form>
<button onclick="document.getElementById('deleteForm').submit();">删除用户信息</button>
```

```java
@RequestMapping(value = "/user/1", method = RequestMethod.DELETE)
public String deleteUser() {
    System.out.println("删除用户信息");
    return "success";
}
```



改：

```html
<form th:action="@{/user}" method="post">
    <input type="hidden" name="_method" value="put">
    用户名： <input type="text" name="username"><br>
    密 码：<input type="password" name="password"><br>
    <input type="submit" value="修改">
</form>
```

```java
@RequestMapping(value = "/user", method = RequestMethod.PUT)
public String updateUser(
        @RequestParam("username") String username,
        @RequestParam("password") String password
) {
    System.out.println("修改用户信息：" + username + " " + password);
    return "success";
}
```



查：

```html
<a th:href="@{/user}">查询所有用户信息</a><br>
<a th:href="@{/user/1}">查询用户信息</a><br>
```

```java
@RequestMapping(value = "/user", method = RequestMethod.GET)
public String getAllUser() {
    System.out.println("查询所有用户信息");
    return "success";
}

@RequestMapping(value = "/user/{id}", method = RequestMethod.GET)
public String getUser(
        @PathVariable("id") String id
) {
    System.out.println("查询用户信息：" + id);
    return "success";
}
```





## 八、HttpMessageConverter

HttpMessageConverter，报文信息转换器，将**请求报文转换为Java对象**，或将**Java对象转换为响应报文**

> 请求报文：浏览器 -> 服务器
>
> 响应报文：服务器 -> 浏览器

HttpMessageConverter提供了两个注解和两个类型：

|                            |     注解      |       类       |
| :------------------------: | :-----------: | :------------: |
| 请求体（报文） -> Java对象 | @RequestBody  | RequestEntity  |
| Java对象 -> 响应体（报文） | @ResponseBody | ResponseEntity |



### 1. @RequestBody

@RequestBody可以获取<font color="red">请求体</font>，需要在控制器方法设置一个形参，使用@RequestBody进行标识，当前请求的请求体就会为当前注解所标识的形参赋值

> get 请求不会有请求体，post 请求才会有请求体

```html
<form th:action="@{/TestRequestBody}" method="post">
    用户名：<input type="text" name="username">
    密码：<input type="password" name="password">
    <input type="submit" value="@RequestBody 注解测试">
</form>
```

```java
@RequestMapping("/RequestBody")
public String RequestBody(@RequestBody String req) {
    System.out.println(req);
    return "success";
}
```

> 还可以通过其他方法获取请求参数



### 2. RequestEntity

RequestEntity封装<font color="red">请求报文</font>的一种类型，需要在控制器方法的形参中设置该类型的形参，当前请求的请求报文就会赋值给该形参，可以通过==getHeaders()==获取请求头信息，通过==getBody()==获取请求体信息

```html
<form th:action="@{/TestRequestEntity}" method="post">
    用户名：<input type="text" name="username">
    密码：<input type="password" name="password">
    <input type="submit" value="TestRequestEntity 测试">
</form>
```

```java
@RequestMapping("/TestRequestEntity")
public String TestRequestEntity(RequestEntity<String> requestEntity) {
    System.out.println("requestEntity：" + requestEntity);
    System.out.println("请求头：" + requestEntity.getHeaders());
    System.out.println("请求体：" + requestEntity.getBody());
    return "success";
}
```

```text
requestEntity：<POST http://localhost:8080/SpringMVC_demo4/TestRequestEntity,username=12&password=21,[host:"localhost:8080", connection:"keep-alive", content-length:"23", cache-control:"max-age=0", sec-ch-ua:""Not_A Brand";v="8", "Chromium";v="120", "Google Chrome";v="120"", sec-ch-ua-mobile:"?0", sec-ch-ua-platform:""Windows"", upgrade-insecure-requests:"1", origin:"http://localhost:8080", user-agent:"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36", accept:"text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", sec-fetch-site:"same-origin", sec-fetch-mode:"navigate", sec-fetch-user:"?1", sec-fetch-dest:"document", referer:"http://localhost:8080/SpringMVC_demo4/", accept-encoding:"gzip, deflate, br, zstd", accept-language:"zh-CN,zh;q=0.9", cookie:"Idea-22e28551=e6da7e7c-cc9a-4bdb-9b01-d8a03a4750ec", Content-Type:"application/x-www-form-urlencoded;charset=UTF-8"]>

请求头：[host:"localhost:8080", connection:"keep-alive", content-length:"23", cache-control:"max-age=0", sec-ch-ua:""Not_A Brand";v="8", "Chromium";v="120", "Google Chrome";v="120"", sec-ch-ua-mobile:"?0", sec-ch-ua-platform:""Windows"", upgrade-insecure-requests:"1", origin:"http://localhost:8080", user-agent:"Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/120.0.0.0 Safari/537.36", accept:"text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.7", sec-fetch-site:"same-origin", sec-fetch-mode:"navigate", sec-fetch-user:"?1", sec-fetch-dest:"document", referer:"http://localhost:8080/SpringMVC_demo4/", accept-encoding:"gzip, deflate, br, zstd", accept-language:"zh-CN,zh;q=0.9", cookie:"Idea-22e28551=e6da7e7c-cc9a-4bdb-9b01-d8a03a4750ec", Content-Type:"application/x-www-form-urlencoded;charset=UTF-8"]

请求体：username=12&password=21
```



### 3. @ResponseBody

##### 向浏览器响应数据的方式一：使用原生API：

```java
@RequestMapping("/TestResponseAPI")
public void TestResponse(HttpServletResponse response) throws IOException {
    response.setContentType("text/html; charset=UTF-8");
    response.getWriter().write("通过 responseAPI 实现页面响应");
}
```

```html
<a th:href="@{/TestResponseAPI}">通过 responseAPI 实现页面响应测试</a><br>
```



##### 向浏览器响应数据的方式二：使用 @ResponseBody 注解：

```java
@RequestMapping("/TestResponse")
@ResponseBody
public String TestResponse() {
    return "通过 @ResponseBody 实现页面响应";
}
```

```html
<a th:href="@{/TestResponse}">通过 responseAPI 实现页面响应测试</a><br>
```



##### 向浏览器响应非字符串类型数据（json）：

需要将非字符串类型数据转换为 json 格式

1. 导入jackson的依赖

   ```xml
   <dependency>
     <groupId>com.fasterxml.jackson.core</groupId>
     <artifactId>jackson-databind</artifactId>
     <version>2.16.1</version>
   </dependency>
   ```

2. 在SpringMVC的核心配置文件 SpringMVC.xml 中开启mvc的注解驱动

   ```xml
   <mvc:annotation-driven />
   ```

3. 在处理器方法上使用 ==@ResponseBody== 注解进行标识

4. 将Java对象直接作为控制器方法的返回值返回，就会自动转换为Json格式的**字符串**

   ```java
   @RequestMapping("/TestResponseUSer")
   @ResponseBody
   public User TestResponseUSer() {
       return new User("1", "admin", "123", "男", 18);
   }
   ```

浏览器显示结果：

```text
{"id":"1","name":"admin","password":"123","gender":"男","age":18}
```



### 4. @RestController

@RestController注解是springMVC提供的一个复合注解，标识在控制器的类上，就相当于为类添加了@Controller注解，并且为其中的每个方法添加了@ResponseBody注解

 

### 5. ResponseEntity

ResponseEntity用于控制器方法的返回值类型，该控制器方法的返回值就是响应到浏览器的响应报文



## 九、文件上传和下载

### 1. 文件上传

```html
<form th:action="@{/testUpload}" method="post" enctype="multipart/form-data">
    <input type="file" name="filename"><br>
    <input type="submit" value="上传"><br>
</form>
```

<font color="orange">***TODO***</font>



### 2. 文件下载	

![66bf3c94d9c307b7e9a62952](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.3k7xxxt438.webp)

```java
// 下载
@RequestMapping("/testDown")
public ResponseEntity<byte[]> testResponseEntity(HttpSession session) throws IOException {
    //获取ServletContext对象
    ServletContext servletContext = session.getServletContext();
    //获取服务器中文件的真实路径
    String realPath = servletContext.getRealPath("/static/img/1.png");
    //创建输入流
    InputStream is = new FileInputStream(realPath);
    //创建字节数组
    byte[] bytes = new byte[is.available()];
    //将流读到字节数组中
    is.read(bytes);
    //创建HttpHeaders对象设置响应头信息
    MultiValueMap<String, String> headers = new HttpHeaders();
    //设置要下载方式以及下载文件的名字
    headers.add("Content-Disposition", "attachment;filename=1.jpg");
    //设置响应状态码
    HttpStatus statusCode = HttpStatus.OK;
    //创建ResponseEntity对象
    ResponseEntity<byte[]> responseEntity = new ResponseEntity<>(bytes, headers, statusCode);
    //关闭输入流
    is.close();
    return responseEntity;
}
```



## 十、拦截器

### 1. 拦截器的配置

SpringMVC中的拦截器用于拦截控制器方法的执行

SpringMVC中的拦截器需要实现 `HandlerInterceptor` 接口

```java
public class testInterceptor implements HandlerInterceptor {
    /**
     * 重写了方法
     */
    @Override
    public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
        System.out.println("preHandle 方法被调用");
        return true; // 表示拦截还是放行
    }

    @Override
    public void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView) throws Exception {
        System.out.println("postHandle 方法被调用");
    }

    @Override
    public void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex) throws Exception {
        System.out.println("afterCompletion 方法被调用");
    }
}
```

##### 方法一：

创建了类还要在 SpringMVC.xml 中进行拦截器注册

```xml
<!--配置拦截器-->
<mvc:interceptors>
    <bean class="com.chr.SpringMVC.interceptor.testInterceptor"/>
</mvc:interceptors>
```

##### 方法二：

将拦截器方法给Spring管理，转为Bean，即加上 `@Component` 注解

```xml
<mvc:interceptors>
    <ref bean="testInterceptor"/>
</mvc:interceptors>
```



<font color="red">两种方法效果一样的，无法设置拦截路径</font>

##### 方法三：

```xml
<mvc:interceptors>
    <mvc:interceptor>
        <mvc:mapping path="/*"/> <!--指定拦截路径-->
        <mvc:exclude-mapping path="/"/> <!--将个别路径排除-->
        <bean class="com.chr.SpringMVC.interceptor.testInterceptor"/> <!--指定拦截器-->
    </mvc:interceptor>
</mvc:interceptors>
```

这个配置文件实现了对于所有路径进行拦截，但是<font color="red">排除了访问首页的路径</font>。



### 2. 拦截器的三个抽象方法

> `preHandle`：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，返回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法

> `postHandle`：控制器方法执行之后执行postHandle()

> `afterComplation`：处理完视图和模型数据，渲染视图完毕之后执行



### 3. 多个拦截器的执行顺序

1. 若每个拦截器的preHandle()都返回true

​			preHandle()会按照拦截器在SpringMVC的配置文件的配置顺序执行，而postHandle()和     afterComplation()会按照配置的反序执行

1. 若某个拦截器的preHandle()返回了false

​			preHandle()返回false和它之前的拦截器的preHandle()都会执行，postHandle()都不执行，返回false的拦截器之前的拦截器的afterComplation()会执行



## 十一、异常处理器

> SpringMVC提供了一个处理控制器方法执行过程中所出现的异常的接口：HandlerExceptionResolver

![image](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image.4g4fq2jla3.webp)

`SimpleMappingExceptionResolver` 类是用于自己写异常处理器来继承的。

`DefaultHandlerExceptionResolver` 类是 SpringMVC 默认的异常处理器。

### 1. 基于配置的异常处理

```xml
<bean class="org.springframework.web.servlet.handler.SimpleMappingExceptionResolver">
    <property name="exceptionMappings">
        <props>
            <prop key="异常名称">跳转的页面名称</prop>
        </props>
    </property>
    <property name="exceptionAttribute" value="ex"/>
</bean>
```

<font color="blue">当出现对应异常时就会跳转到对应的页面上</font>

`<property name="exceptionAttribute" value="ex"/>`可选，写上后会获取错误信息，以  value 为键，错误信息为值存在 Request 域中。

`<p th:text="${ex}"></p>`可获取展示出。



### 2. 基于注解的异常处理

```java
@ControllerAdvice
public class testExceptionHandling {
    @ExceptionHandler(ArithmeticException.class)
    public String handleArithmeticException(Exception ex, Model model) {
        model.addAttribute("ex", ex);
        return "bug";
    }
}
```



## 十二、注解配置SpringMVC

在Servlet3.0环境中，容器会在类路径中查找实现javax.servlet.ServletContainerInitializer接口的类，如果找到的话就用它来配置Servlet容器。
Spring提供了这个接口的实现，名为SpringServletContainerInitializer，这个类反过来又会查找实现WebApplicationInitializer的类并将配置的任务交给它们来完成。Spring3.2引入了一个便利的WebApplicationInitializer基础实现，名为AbstractAnnotationConfigDispatcherServletInitializer，当我们的类扩展了AbstractAnnotationConfigDispatcherServletInitializer并将其部署到Servlet3.0容器的时候，容器会自动发现它，并用它来配置Servlet上下文。

### 1. 创建初始化类，代替web.xml

```java
import jakarta.servlet.Filter;
import org.springframework.web.filter.CharacterEncodingFilter;
import org.springframework.web.filter.HiddenHttpMethodFilter;
import org.springframework.web.servlet.support.AbstractAnnotationConfigDispatcherServletInitializer;

/**
 * @Author: 程浩然
 * @Create: 2024/9/4 - 13:51
 * @Description: web 层配置类，代替web.xml
 */
public class WebInit extends AbstractAnnotationConfigDispatcherServletInitializer {

    /**
     * 指定 Spring 配置类
     */
    @Override
    protected Class<?>[] getRootConfigClasses() {
        return new Class[]{SpringConfig.class};
    }

    /**
     * 指定 SpringMVC 配置类
     */
    @Override
    protected Class<?>[] getServletConfigClasses() {
        return new Class[]{WebConfig.class};
    }

    /**
     * 指定 DispatcherServlet 的映射规则，即 url-pattern
     */
    @Override
    protected String[] getServletMappings() {
        return new String[]{"/"};
    }

    /**
     * 添加过滤器
     */
    @Override
    protected Filter[] getServletFilters() {
        // 字符过滤器
        CharacterEncodingFilter characterEncodingFilter = new CharacterEncodingFilter();
        characterEncodingFilter.setEncoding("UTF-8");
        characterEncodingFilter.setForceResponseEncoding(true);

        // HiddenHttpMethodFilter 过滤器（用于将get和post请求转换为put和delete请求）
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        return new Filter[]{characterEncodingFilter, hiddenHttpMethodFilter};
    }
}
```



### 2. 创建SpringConfig配置类，代替spring的配置文件

```java
@Configuration
public class SpringConfig {
	//ssm整合之后，spring的配置信息写在此类中
}
```



### 3. 创建WebConfig配置类，代替SpringMVC的配置文件

```java
package com.chr.SpringMVC.config;

import com.chr.SpringMVC.initerceptor.interceptor_one;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.multipart.MultipartResolver;
import org.springframework.web.multipart.support.StandardServletMultipartResolver;
import org.springframework.web.servlet.HandlerExceptionResolver;
import org.springframework.web.servlet.ViewResolver;
import org.springframework.web.servlet.config.annotation.*;
import org.springframework.web.servlet.handler.SimpleMappingExceptionResolver;
import org.thymeleaf.spring6.SpringTemplateEngine;
import org.thymeleaf.spring6.templateresolver.SpringResourceTemplateResolver;
import org.thymeleaf.spring6.view.ThymeleafViewResolver;
import org.thymeleaf.templateresolver.ITemplateResolver;

import java.util.List;
import java.util.Properties;

/**
 * @Author: 程浩然
 * @Create: 2024/9/4 - 13:56
 * @Description: SpringMVC 配置类
 */

/*
 * 1.扫描组件  2.视图解析器  3.view-Controller  4.default-Servlet-handler
 * 5.mvc注解驱动 6.文件上传解析器  7.异常处理 8.拦截器
 */

@Configuration
// 1.扫描组件
@ComponentScan("com.chr.SpringMVC.controller")
// 5.mvc注解驱动
@EnableWebMvc
public class WebConfig implements WebMvcConfigurer {
    // 2.视图解析器
    //配置生成模板解析器
    @Bean
    public ITemplateResolver templateResolver() {
        SpringResourceTemplateResolver templateResolver = new SpringResourceTemplateResolver();
        templateResolver.setPrefix("/WEB-INF/templates/");
        templateResolver.setSuffix(".html");
        templateResolver.setTemplateMode("HTML");
        templateResolver.setCharacterEncoding("UTF-8");
        return templateResolver;
    }

    //生成模板引擎并为模板引擎注入模板解析器
    @Bean
    public SpringTemplateEngine templateEngine(ITemplateResolver templateResolver) {
        SpringTemplateEngine templateEngine = new SpringTemplateEngine();
        templateEngine.setTemplateResolver(templateResolver);
        return templateEngine;
    }

    //生成视图解析器并未解析器注入模板引擎
    @Bean
    public ViewResolver viewResolver(SpringTemplateEngine templateEngine) {
        ThymeleafViewResolver viewResolver = new ThymeleafViewResolver();
        viewResolver.setOrder(1);
        viewResolver.setCharacterEncoding("UTF-8");
        viewResolver.setTemplateEngine(templateEngine);
        return viewResolver;
    }

    // 4.default-Servlet-handler
    @Override
    public void configureDefaultServletHandling(DefaultServletHandlerConfigurer configurer) {
        configurer.enable();
    }

    // 8.拦截器
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        // addInterceptor(): 添加拦截器
        // addPathPatterns(): 添加拦截路径
        // excludePathPatterns(): 移除拦截路径
        registry.addInterceptor(new interceptor_one()).addPathPatterns("/");
    }

    // 3.view-Controller
    @Override
    public void addViewControllers(ViewControllerRegistry registry) {
        registry.addViewController("/hello").setViewName("hello");
    }

    // 6.文件上传解析器
    @Bean
    public MultipartResolver multipartResolver() {
        MultipartResolver multipartResolver = new StandardServletMultipartResolver();
        return multipartResolver;
    }


    // 7.异常处理
    @Override
    public void configureHandlerExceptionResolvers(List<HandlerExceptionResolver> resolvers) {
        SimpleMappingExceptionResolver resolver = new SimpleMappingExceptionResolver();
        Properties prop = new Properties();
        prop.setProperty("java.lang.ArithmeticException", "bug.html");
        resolver.setExceptionMappings(prop);
        resolver.setExceptionAttribute("ex");
        resolvers.add(resolver);
    }
}
```



### 创建一个SpringMVC项目流程：

1. 新建模块

2. 导入jar包

3. 创建 webapp-WEB-INF 文件夹

4. 编写 web.xml 和 springMVC.xml 配置文件（或创建配置类）

    

## 十三、SpringMVC执行流程

![springmvc执行流程图](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/springmvc执行流程图.969orabu85.webp)

### 1. SpringMVC常用组件

- `DispatcherServlet`：**前端控制器**，不需要工程师开发，由框架提供

作用：统一处理请求和响应，整个流程控制的中心，由它调用其它组件处理用户的请求

- `HandlerMapping`：**处理器映射器**，不需要工程师开发，由框架提供

作用：根据请求的url、method等信息查找Handler，即控制器方法

- `Handler`：**处理器**，需要工程师开发

作用：在DispatcherServlet的控制下Handler对具体的用户请求进行处理

- `HandlerAdapter`：**处理器适配器**，不需要工程师开发，由框架提供

作用：通过HandlerAdapter对处理器（控制器方法）进行执行

- `ViewResolver`：**视图解析器**，不需要工程师开发，由框架提供

作用：进行视图解析，得到相应的视图，例如：ThymeleafView、InternalResourceView、RedirectView

- `View`：**视图**



### 2. SpringMVC的执行流程

1) 用户向服务器发送请求，请求被SpringMVC 前端控制器 DispatcherServlet捕获。

2) DispatcherServlet对请求URL进行解析，得到请求资源标识符（URI），判断请求URI对应的映射：

a) 不存在

i. 再判断是否配置了mvc:default-servlet-handler

ii. 如果没配置，则控制台报映射查找不到，客户端展示404错误

iii. 如果有配置，则访问目标资源（一般为静态资源，如：JS,CSS,HTML），找不到客户端也会展示404错误

b) 存在则执行下面的流程

3) 根据该URI，调用HandlerMapping获得该Handler配置的所有相关的对象（包括Handler对象以及Handler对象对应的拦截器），最后以HandlerExecutionChain执行链对象的形式返回。

4) DispatcherServlet 根据获得的Handler，选择一个合适的HandlerAdapter。

5) 如果成功获得HandlerAdapter，此时将开始执行拦截器的preHandler(…)方法【正向】

6) 提取Request中的模型数据，填充Handler入参，开始执行Handler（Controller)方法，处理请求。在填充Handler的入参过程中，根据你的配置，Spring将帮你做一些额外的工作：

a) HttpMessageConveter： 将请求消息（如Json、xml等数据）转换成一个对象，将对象转换为指定的响应信息

b) 数据转换：对请求消息进行数据转换。如String转换成Integer、Double等

c) 数据格式化：对请求消息进行数据格式化。 如将字符串转换成格式化数字或格式化日期等

d) 数据验证： 验证数据的有效性（长度、格式等），验证结果存储到BindingResult或Error中

7) Handler执行完成后，向DispatcherServlet 返回一个ModelAndView对象。

8) 此时将开始执行拦截器的postHandle(...)方法【逆向】。

9) 根据返回的ModelAndView（此时会判断是否存在异常：如果存在异常，则执行HandlerExceptionResolver进行异常处理）选择一个适合的ViewResolver进行视图解析，根据Model和View，来渲染视图。

10) 渲染视图完毕执行拦截器的afterCompletion(…)方法【逆向】。

11) 将渲染结果返回给客户端。
