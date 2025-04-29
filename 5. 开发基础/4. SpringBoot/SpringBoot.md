## 一. SpringBoot 入门

#### 1. 创建SpringBoot项目

##### 开发小技巧

1. Lombok：可以生成get，set，toString方法

   ```java
   @ToString // 生成toString方法
   @Data // 生成get，set方法
   @NoArgsConstructor // 生成无参构造器
   @AllArgsConstructor // 生成全参构造器
   ```

2. Spring Initailizr（项目初始化向导）

3. 加入`@Slf4j`注解后可以通过`log.info(name={}，name)`方法将信息以日志方式打印输出





#### 2. 添加 Maven 依赖

#### 3. 编写启动项

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```



#### 4. 写HelloWOrld

```java
package com.chr.springboot;

import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class Hello {
    @RequestMapping("/hello")
    public String handle01(){
        return "Hello, Spring Boot 2!";
    }
}
```



## 二. 依赖管理，自动配置，容器功能

### 1. 依赖管理

继承父项目Maven

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>3.3.4</version>
    <relativePath/>
</parent>
```

如果想修改版本：

```xml
<properties>
    <mysql.version>5.1.43</mysql.version>
</properties>
```





### 2. 自动配置

- 自动配好Tomcat

- - 引入Tomcat依赖
  - 配置Tomcat

```xml
<dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-tomcat</artifactId>
      <version>2.3.4.RELEASE</version>
      <scope>compile</scope>
</dependency>
```



- 自动配好SpringMVC

- - 引入SpringMVC全套组件
  - 自动配好SpringMVC常用组件

- 

- 自动配好Web常见功能，如：字符编码问题

- - SpringBoot帮我们配置好了所有web开发的常见场景

- 

- 默认的包结构

- - 主程序所在包及其下面的所有子包里面的组件都会被默认扫描进来
  - 无需以前的包扫描配置
  - 想要改变扫描路径，@SpringBootApplication(scanBasePackages=**"com.chr"**)

- - 或者@ComponentScan 指定扫描路径

```java
@SpringBootApplication
等同于
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan("com.chr.boot")
```



- 各种配置拥有默认值

- - 默认配置最终都是映射到某个类上，如：MultipartProperties
  - 配置文件的值最终会绑定每个类上，这个类会在容器中创建对象

- 

- 按需加载所有自动配置项

- - 非常多的starter
  - 引入了哪些场景这个场景的自动配置才会开启
  - SpringBoot所有的自动配置功能都在 spring-boot-autoconfigure 包里面

  

### 3. 容器功能

#### 3.1 组件添加 

> 方式一：通过xml文件配置

> 方式二：@Bean ，@Component，@Service，@Controller，@Repository
>
> @Bean是加在方法上，将返回值作为容器交给Spring管理
>
> @Component用于普通对象
>
> @Service用于Service层
>
> @Controller用于web层
>
> @Repository用于dao层

> 方法三：通过@Configuration注解为配置类
> 有一个属性：proxyBeanMethods = false，用来设置是否是代理 Bean 的方法
>
> Full（proxyBeanMethods = true）：代理模式，保证每个@Bean方法被调用多少次返回的组件都是单实例的
>
> Lite（proxyBeanMethods = false）：不代理模式，每个@Bean方法被调用多少次返回的组件都是新创建的
>
> <font color="red">组件依赖必须使用Full模式，其他默认是Lite模式</font>

> 方法四：@ComponentScan、@Import 将类导入，给容器中自动创建出这类型的组件、默认组件的名字就是全类名

> 方法五：@Conditional 条件装配：满足Conditional指定的条件，则进行组件注入。
>
> @Conditional 是一个基本注解，有许多更多判断条件的注解

```java
@ConditionalOnBean(name = "cat") // 只有存在cat组件时在将tom注册到容器中
    @Bean(value = "tom")
    public String getTom() {
        return "tom";
    }

//    @Bean(value = "cat")
    public String getCat() {
        return "cat";
    }
```





#### 3.2 原生配置文件引入

**==@ImportResource==** 用于将 xml 配置文件注入到 Spring 容器中（针对不想讲xml文件改为java文件的）

```xml
======================beans.xml=========================
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd http://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

    <bean id="haha" class="com.atguigu.boot.bean.User">
        <property name="name" value="zhangsan"></property>
        <property name="age" value="18"></property>
    </bean>

    <bean id="hehe" class="com.atguigu.boot.bean.Pet">
        <property name="name" value="tomcat"></property>
    </bean>
</beans>
```

```java
// 将 beans.xml 注入到容器中
@ImportResource("classpath:beans.xml")
public class MyConfig {}
```





#### 3.3 配置绑定（信息注入）

##### 1. @ConfigurationProperties + @Component

==@Component==注解将类加入到容器中

==@ConfigurationProperties（prefix = "在配置文件中的前缀"）== 注解可以将配置信息自动导入到<font color="red">已经在容器中的 Bean</font>

```java
@Autowired
car car;

@RequestMapping("/car")
public car car() {
    return car;
}
```

```java
@Data
@Component
@ConfigurationProperties(prefix = "a")
public class car {
    String id;
    String name;
}
```

```properties
a.id=id
a.name=name
```



##### 2. @EnableConfigurationProperties + @ConfigurationProperties

==@EnableConfigurationProperties（类名.class）==在<font color="red">配置类上写</font>，指明开启属性绑定并且将该类加入容器中

==@ConfigurationProperties(prefix="在配置文件中的前缀")==在类上写

```java
@Configuration(proxyBeanMethods = false)
@EnableConfigurationProperties(car.class)
public class MybatisConfig {}
```

```java
@Data
@ConfigurationProperties(prefix = "a")
public class car {
    String id;
    String name;
}
```





## 三. 自动配置原理

`@SpringBootApplication`注释相当于：`@SpringBootConfiguration`,`@EnableAutoConfiguration`,`@ComponentScan`这三个注解。

```java
@SpringBootConfiguration
@EnableAutoConfiguration
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
```

### 1. `@SpringBootConfiguration`

表示当前是一个配置类



### 2. `@ComponentScan`

```java
@ComponentScan(
    excludeFilters = {@Filter(
    type = FilterType.CUSTOM,
    classes = {TypeExcludeFilter.class}
), @Filter(
    type = FilterType.CUSTOM,
    classes = {AutoConfigurationExcludeFilter.class}
)}
)
```

指定扫描哪些Spring注解



### 3. `@EnableAutoConfiguration`

`@EnableAutoConfiguration`注解相当于`@AutoConfigurationPackage`+`@Import({AutoConfigurationImportSelector.class})`这两个注解。



#### 3.1 `@AutoConfigurationPackage`注解

```java
@Import({AutoConfigurationPackages.Registrar.class})
public @interface AutoConfigurationPackage {
    String[] basePackages() default {};

    Class<?>[] basePackageClasses() default {};
}
```

自动配置包。

批量注册，将MainApplication所在的包的组件导入进来。



#### 3.2 `@Import({AutoConfigurationImportSelector.class})`注解

会从写死的配置文件`org.springframework.boot.spring-boot\3.3.4\spring-boot-3.3.4.jar!\META-INF\spring.factories`中读取要加入的组件，总共127个。但是并不是每次都需要加载127个，按需开启自动配置项：按照条件装配规则（@Conditional），最终会按需配置。

<font color="red">SpringBoot启动会全部加载，但是最终按照条件装配按需配置</font>



### 4. 总结：

- SpringBoot先加载所有的自动配置类  xxxxxAutoConfiguration
- 每个自动配置类按照条件进行生效，默认都会绑定配置文件指定的值。xxxxProperties里面拿。xxxProperties和配置文件进行了绑定
- 生效的配置类就会给容器中装配很多组件
- 只要容器中有这些组件，相当于这些功能就有了
- 定制化配置

- - 用户直接自己@Bean替换底层的组件
  - 用户去看这个组件是获取的配置文件什么值就去修改(更常用)

<font color="red">**xxxxxAutoConfiguration ---> 组件  --->** **xxxxProperties里面拿值  ----> application.properties**</font>



## 四. 配置文件

### 1. yaml 基本语法

- key: value；kv之间有空格
- 大小写敏感
- 使用缩进表示层级关系
- 缩进不允许使用tab，只允许空格
- 缩进的空格数不重要，只要相同层级的元素左对齐即可
- '#'表示注释
- 字符串无需加引号，如果要加，''与""表示字符串内容 会被 转义/不转义



### 2. 数据类型

1. 字面量：单个的、不可再分的值。date、boolean、string、number、null

   ```yaml
   key: val
   ```

2. 对象：键值对的集合。map，hash，set

    ```yaml
    行内写法：  k: {k1:v1,k2:v2,k3:v3}
    #或
    k: 
    	k1: v1
    	k2: v2
    	k3: v3
    ```
    
3. 数组：一组按次序排列的值。array，list

    ```yaml
    行内写法：  k: [v1,v2,v3]
    #或者
    k:
     - v1
     - v2
     - v3
    ```



### 3. 实际运用

```java
@Data
public class Person {
	
	private String userName;
	private Boolean boss;
	private Date birth;
	private Integer age;
	private Pet pet;
	private String[] interests;
	private List<String> animal;
	private Map<String, Object> score;
	private Set<Double> salarys;
	private Map<String, List<Pet>> allPets;
}

@Data
public class Pet {
	private String name;
	private Double weight;
}
```

转换为yaml：

```yaml
# yaml表示以上对象
person:
  userName: zhangsan
  boss: false
  birth: 2019/12/12 20:12:33
  age: 18
  pet: 
    name: tomcat
    weight: 23.4
  interests: [篮球,游泳]
  animal: 
    - jerry
    - mario
  score:
    english: 
      first: 30
      second: 40
      third: 50
    math: [131,140,148]
    chinese: {first: 128,second: 136}
  salarys: [3999,4999.98,5999.99]
  allPets:
    sick:
      - {name: tom}
      - {name: jerry,weight: 47}
    health: [{name: mario,weight: 47}]
```



## 五. Web开发

### 1. 简单功能分析

#### 1.1 静态资源目录

将静态文件（图片，html文件，css文件，js文件等）放在resources下的/`static` (或 `/public` 或 `/resources` 或 `/META-INF/resources`)目录下可以直接访问

![image](https://ChengHaoRan666.github.io/picx-images-hosting/SpringBoot/image.7pae6fho2.webp)

访问方式：当前项目根路径/ + 静态资源名 

> 原理：请求进来，先去找 Controller 看能不能处理。不能处理的所有请求又都交给静态资源处理器。静态资源也找不到则响应404页面



改变默认的静态资源路径：

```yaml
# 修改服务器默认的静态资源路径，修改浏览器访问静态资源的前缀
spring:
  mvc:
    # 修改浏览器访问静态资源的前缀
    static-path-pattern: /res/**
    # 修改服务器默认的静态资源路径
  web:
    resources:
      static-locations:
        [ classpath:/haha/ ]
```

注意：<font color="red">服务器默认的静态资源路径不是修改原有的，而是追加在后面</font>



#### 1.2 index页请求

SpringBoot会自动识别在静态资源路径下的index.html文件用于主页显示。

<font color="green">但是如果修改了浏览器访问静态资源的前缀就无法识别到index.html</font>





### 2. 请求参数处理

#### 2.1 REST风格

具体说，就是 HTTP 协议里面，四个表示操作方式的动词：GET、POST、PUT、DELETE。

它们分别对应四种基本操作：

- GET 用来获取资源
- POST 用来新建资源
- PUT 用来更新资源
- DELETE 用来删除资源

REST 风格提倡 URL 地址使用统一的风格设计，从前到后各个单词使用斜杠分开，不使用问号键值对方式携带请求参数，而是将要发送给服务器的数据作为 URL 地址的一部分，以保证整体风格的一致性。

| 操作     | 传统方式         | REST风格                |
| -------- | ---------------- | ----------------------- |
| 查询操作 | getUserById?id=1 | user/1-->get请求方式    |
| 保存操作 | saveUser         | user-->post请求方式     |
| 删除操作 | deleteUser?id=1  | user/1-->delete请求方式 |
| 更新操作 | updateUser       | user-->put请求方式      |





在SpringBoot中要进行手动开启支持REST：

```yaml
spring:
  mvc:
    hiddenmethod:
      filter:
        enabled: true
```

html不直接支持`delete`和`put`请求，需要在内容中加上一个隐藏域，name为`_method`，value为`delete`或`put`。并且`method`只能为`post`。



示例：

```html
<form th:action="@{/indexTest}" method="post">
    <!--隐藏域-->
    <input type="hidden" name="_method" value="delete">
    username： <input type="text" name="username">
    password： <input type="text" name="password">
    <input type="submit" name="提交">
</form>
```



映射的java方法可以使用以下注解：

> @RequestMapping(value = "indexTest", method = RequestMethod.DELETE)
>
> @DeleteMapping(value = "indexTest")
>
> @PutMapping(value = "indexTest")
>
> @GetMapping(value = "indexTest")
>
> @PostMapping(value = "indexTest")

下面四个是对`@RequestMapping`进行封装，自己添加了`method`为什么值



#### 2.2 REST原理

```java
// HiddenHttpMethodFilter.java
protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
        HttpServletRequest requestToUse = request;
        if ("POST".equals(request.getMethod()) && request.getAttribute("jakarta.servlet.error.exception") == null) {
            String paramValue = request.getParameter(this.methodParam);
            if (StringUtils.hasLength(paramValue)) {
                String method = paramValue.toUpperCase(Locale.ENGLISH);
                if (ALLOWED_METHODS.contains(method)) {
                    requestToUse = new HttpMethodRequestWrapper(request, method);
                }
            }
        }

        filterChain.doFilter((ServletRequest)requestToUse, response);
    }
```

1. 当进行提交后进行过滤，先将Request赋值给requestToUse
2. 判断Request的提交方法是不是`POST`并且判断是否有异常，满足条件继续
3. `request.getParameter(this.methodParam);`获取提交的时候传递的提交方式
4. 判断提交方式是否为空字符串或为null，不是继续
5. 判断提交方式是否在`ALLOWED_METHODS`中，如果在继续
6. 创建`HttpMethodRequestWrapper`，将Request的提交方式替换为真正的提交方式
7. 当前过滤器功能完成，继续过滤





#### 2.3 自定义提交方式的参数

默认是`_method`，原因是`this.methodParam`获取的是`methodParam`，这个值初始化为`_method`，我们可以自定义一个组件通过`setMethodParam`方法修改`this.methodParam`。

```java
@Configuration
class Configuration{
    @Bean
    public HiddenHttpMethodFilter hiddenHttpMethodFilter() {
        HiddenHttpMethodFilter hiddenHttpMethodFilter = new HiddenHttpMethodFilter();
        hiddenHttpMethodFilter.setMethodParam("chr"); // 设置自定义参数名
        return hiddenHttpMethodFilter;
    }
}
```



#### 2.4 请求映射原理

`org.springframework.web.servlet.DispatcherServlet`管理请求映射。其间接继承在HttpServlet中，方法`doService`处理请求，在`doService`中又调用`doDispatch`方法。

```java
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    HttpServletRequest processedRequest = request;
    HandlerExecutionChain mappedHandler = null;
    boolean multipartRequestParsed = false;
    WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);

    try {
        try {
            ModelAndView mv = null;
            Exception dispatchException = null;

            try {
                processedRequest = this.checkMultipart(request);
                multipartRequestParsed = processedRequest != request;
                mappedHandler = this.getHandler(processedRequest);
                if (mappedHandler == null) {
                    this.noHandlerFound(processedRequest, response);
                    return;
                }

                HandlerAdapter ha = this.getHandlerAdapter(mappedHandler.getHandler());
                String method = request.getMethod();
                boolean isGet = HttpMethod.GET.matches(method);
                if (isGet || HttpMethod.HEAD.matches(method)) {
                    long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
                    if ((new ServletWebRequest(request, response)).checkNotModified(lastModified) && isGet) {
                        return;
                    }
                }

                if (!mappedHandler.applyPreHandle(processedRequest, response)) {
                    return;
                }

                mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
                if (asyncManager.isConcurrentHandlingStarted()) {
                    return;
                }

                this.applyDefaultViewName(processedRequest, mv);
                mappedHandler.applyPostHandle(processedRequest, response, mv);
            } catch (Exception var20) {
                dispatchException = var20;
            } catch (Throwable var21) {
                dispatchException = new ServletException("Handler dispatch failed: " + var21, var21);
            }

            this.processDispatchResult(processedRequest, response, mappedHandler, mv, (Exception)dispatchException);
        } catch (Exception var22) {
            triggerAfterCompletion(processedRequest, response, mappedHandler, var22);
        } catch (Throwable var23) {
            triggerAfterCompletion(processedRequest, response, mappedHandler, new ServletException("Handler processing failed: " + var23, var23));
        }

    } finally {
        if (asyncManager.isConcurrentHandlingStarted()) {
            if (mappedHandler != null) {
                mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
            }

            asyncManager.setMultipartRequestParsed(multipartRequestParsed);
        } else if (multipartRequestParsed || asyncManager.isMultipartRequestParsed()) {
            this.cleanupMultipart(processedRequest);
        }

    }
}
```

在`mappedHandler = this.getHandler(processedRequest);`中找到了处理请求的方法。



#### 2.5 请求参数获取

1. `@PathVariable`
   可以获取url的参数
   ==@PathVariable Map<String, String> map==可以获取所有参数

   ```java
   @RequestMapping("/car/{id}/name/{name}")
       public String test1(@PathVariable("id") String id, 
                           @PathVariable("name") String name, 
                           @PathVariable Map<String, String> map) {
           System.out.println(map);
           return id + " " + name;
       }
   ```

2. `@RequestHeader `
   可以获取请求头信息
   ==@RequestHeader Map<String, Object> map==可以获取全部请求头信息

   ```java
   @RequestMapping("/car/{id}/name/{name}")
       public Map<String, Object> test2(
               @RequestHeader("host") String host,
               @RequestHeader Map<String, Object> map) {
           Map<String, Object> map1 = new HashMap<>();
   
           map1.put("map", map);
           map1.put("host", host);
           return map1;
       }
   ```

3. `@RequestParam`
   `required`属性可以决定这个值是不是必须的，true：必须，false：非必须

   ```java
   <!--请求：http://localhost:8080/test3?age=1&lists=1&lists=2-->
   @RequestMapping("/test3")
       public Map<String, Object> test3(
               @RequestParam("age") Integer age,
               @RequestParam("lists") List<Integer> lists
       ) {
           Map<String, Object> map = new HashMap<>();
           map.put("age", age);
           map.put("lists", lists);
           return map;
       }
   ```

4. `@CookieValue `

   ```java
   @RequestMapping("/test4")
       public Map<String, Object> test4(
               @CookieValue Cookie cookie
       ) {
           Map<String, Object> map = new HashMap<>();
           map.put("cookie", cookie);
           return map;
       }
   ```

5. `@RequestBody`

   ```java
   // 获取请求体
   @RequestMapping("/test5")
       public Map<String, Object> test4(
               @RequestBody String body
       ) {
           Map<String, Object> map = new HashMap<>();
           map.put("body", body);
           return map;
       }
   ```

6. `@RequestAttribute`
   获取 request 域中的值





### 3. 数据响应与内容协商

#### 3.1 SpringMVC支持的返回值

```java
ModelAndView
Model
View
ResponseEntity 
ResponseBodyEmitter
StreamingResponseBody
HttpEntity
HttpHeaders
Callable
DeferredResult
ListenableFuture
CompletionStage
WebAsyncTask
有 @ModelAttribute 且为对象类型的
@ResponseBody 注解 ---> RequestResponseBodyMethodProcessor
```



#### 3.2 内容协商

根据客户端接收能力不同，返回不同媒体类型的数据。

> 只需要改变请求头中Accept字段。Http协议中规定的，告诉服务器本客户端可以接收的数据类型。

默认是根据浏览器自己设置好的类型权重来展示的。

如果想自己设置按照什么方式返回数据类型可以按照下面步骤：

1. 导入相关的Maven依赖

   ```xml
   <!--导入xml请求处理-->
   <dependency>
       <groupId>com.fasterxml.jackson.dataformat</groupId>
       <artifactId>jackson-dataformat-xml</artifactId>
   </dependency>
   ```

2. 配置文件中开启根据参数方式的内容协商

   ```yaml
   spring:
   	mvc:
       	contentnegotiation:
         		favor-parameter: true
   ```

3. 在url中加上一个参数`format`，他的值就是返回类型

例子：

```java
url:http://localhost:8080/json1?format=xml
返回值：
<person>
<id>1</id>
<age>2</age>
<a>a</a>
<b>b</b>
</person>
    
url：http://localhost:8080/json1?format=json
返回值：{"id":1,"age":2,"a":"a","b":"b"}
```





### 4.thymeleaf

#### 1. 基本语法

##### 1. 表达式

| 表达式名字 | 语法   | 用途                               |
| ---------- | ------ | ---------------------------------- |
| 变量取值   | ${...} | 获取请求域、session域、对象等值    |
| 选择变量   | *{...} | 获取上下文对象值                   |
| 消息       | #{...} | 获取国际化等值                     |
| 链接       | @{...} | 生成链接                           |
| 片段表达式 | ~{...} | jsp:include 作用，引入公共页面片段 |



>文本值: 'one text' **,** 'Another one!',…数字: 0 , 34 , 3.0,12.3,…布尔值: true,false
>
>空值: null
>
>变量： one，two，.... 变量不能有空格
>
>字符串拼接: **+**
>
>变量替换: ==|The name is ${name}|== 
>
>运算符: +   -   *   /   %
>
>运算符:  and , or
>
>一元运算: ! , not 
>
>比较: > , < , >= , <= ( gt , lt , ge , le )等式: == , != ( eq , ne ) 
>
>If-then: (if) ? (then)
>
>If-then-else: (if) ? (then) : (else)
>
>Default: (value) ?: (defaultvalue) 



#### 2. 设置属性值-th:attr

在属性前面加上`th:`



#### 3. 迭代

```html
<tr th:each="prod : ${prods}">
        <td th:text="${prod.name}">Onions</td>
        <td th:text="${prod.price}">2.41</td>
        <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>
```

```html
<tr th:each="prod,iterStat : ${prods}" th:class="${iterStat.odd}? 'odd'">
  <td th:text="${prod.name}">Onions</td>
  <td th:text="${prod.price}">2.41</td>
  <td th:text="${prod.inStock}? #{true} : #{false}">yes</td>
</tr>
```





#### 4. 条件运算

```html
<a href="comments.html"
th:href="@{/product/comments(prodId=${prod.id})}"
th:if="${not #lists.isEmpty(prod.comments)}">view</a>
```



<font color="red">当返回的视图名称以forward:为前缀，转发</font>

<font color="red">当返回的视图名称redirect:为前缀，重定向</font>



### 5. 拦截器

#### 5.1 实现拦截器

拦截器需要实现`HandlerInterceptor`接口，`HandlerInterceptor`接口提供三个方法：

> `preHandle`：控制器方法执行之前执行preHandle()，其boolean类型的返回值表示是否拦截或放行，返回true为放行，即调用控制器方法；返回false表示拦截，即不调用控制器方法

> `postHandle`：控制器方法执行之后但页面还没有进行渲染时执行postHandle()

> `afterComplation`：处理完视图和模型数据，渲染视图完毕之后执行

```java
@Override
public boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler) throws Exception {
    return true; // 放行
    return false;// 拦截
}
```

<font color="red">拦截器不需要加入到spring中，在配置拦截器时会加入</font>





#### 5.2 配置拦截器

通过实现`WebMvcConfigurer`接口来配置拦截器：

```java
@Configuration
public class webConfig implements WebMvcConfigurer {
    @Override
    public void addInterceptors(InterceptorRegistry registry) {
        registry.addInterceptor(new HandlerInterceptorTest()) // 配置拦截器
                .addPathPatterns("") // 配置拦截路径
                .excludePathPatterns(""); // 配置放行路径
    }
}
```

> 注意`/**`是会将所有请求都拦截了，同时也会拦截静态资源，要注意给静态资源放行



#### 原理：

![image](https://ChengHaoRan666.github.io/picx-images-hosting/SpringBoot/image.5c0z6adria.png)

挨个执行每个拦截器的`preHandle`方法，没有错误再执行目标方法，执行完目标方法后<font color="red">倒序</font>执行`postHandle`方法。进行页面渲染，渲染成功后再<font color="red">倒序</font>执行`afterCompletion`方法。期间一旦有某个方法出错就会立即调用该拦截器的`afterCompletion`方法，没有执行过`preHandle`方法的拦截器不必执行`afterCompletion`方法。





### 6. 文件上传与下载

<font color="red">文件上传的`method`和`enctype`固定的，不可更改</font>

#### 6.1 单文件上传

```html
<form th:action="@{/testUpload1}" method="post" enctype="multipart/form-data">
    <input type="file" name="filename"><br>
    <input type="submit" value="单文件上传"><br>
</form>
```



可以使用`@RequestPart("")`获取上传的文件，文件类型`MultipartFile`，通过`MultipartFile` 的方法获取文件参数（`getOriginalFilename`，`getSize`方法），保存到本地（`transferTo`方法）。

```java
// 单文件上传
    @PostMapping("testUpload1")
    public String testUpload1(@RequestPart("filename") MultipartFile file) throws IOException {
      log.info("文件名{}，文件大小{}", file.getOriginalFilename(), file.getSize());
        if (!file.isEmpty()) {
         // 保存到服务器
         file.transferTo(new File("D:\\single\\" + file.getOriginalFilename()));
        }
        return "success";
    }
```







#### 6.2 多文件上传

```html
<form th:action="@{/testUpload2}" method="post" enctype="multipart/form-data">
    <input type="file" name="filename" multiple><br>
    <input type="submit" value="多文件上传"><br>
</form>
```

> 加上==multiple==允许选中多个文件



```java
// 多文件上传
@PostMapping("testUpload2")
public String testUpload2(@RequestPart("filenames") MultipartFile[] files) throws IOException {
    for (MultipartFile file : files) {
      log.info("文件名{}，文件大小{}", file.getOriginalFilename(), file.getSize());
        if (!file.isEmpty()) {
         file.transferTo(new File("D:\\double\\" + file.getOriginalFilename()));
        }
    }
    return "success";
}
```


>文件上传初始单个最大大小为1M，全部文件初始最大10M，如果超过需要重新配置

```yaml
spring:
    servlet:
      multipart:
          max-file-size: 10MB #修改上传文件时单个文件大小
          max-request-size: 100MB #修改上传文件时全部文件大小
```






### 7. 异常处理

#### 7.1 自定义异常页面

在静态页面下创建`error`文件夹，会自动识别对应的错误页面，有精确的错误状态码页面就匹配精确，没有就找 4xx.html，5xx.html；如果都没有就触发白页

![image](https://ChengHaoRan666.github.io/picx-images-hosting/SpringBoot/image.8ad99w9pl3.webp)





#### 7.2 使用`@ControllerAdvice`注解

```java
@ControllerAdvice
public class errorTest {
    @ExceptionHandler(ArithmeticException.class)
    public ResponseEntity<String> handleArithmeticException(Exception ex) {
        return ResponseEntity.status(HttpStatus.INTERNAL_SERVER_ERROR).body(ex.getMessage());
    }
}
```



也可以这样：

```java
@RestControllerAdvice
public class errorTest {
    @ExceptionHandler(ArithmeticException.class)
    public String handleArithmeticException(Exception ex) {
        return ex.toString();
    }
}
```



<font color="red">如果采用第二种方法会覆盖掉第一种方法，建议在开发时使用第二种方法便于查找异常，在应用中使用第一种方法，考虑客户体验</font>





### 8. Web原生组件注入（Servlet、Filter、Listener）

#### 8.1. 使用Servlet API

@ServletComponentScan(basePackages = "com.atguigu.admin") :指定原生Servlet组件都放在那里

@WebServlet(urlPatterns = "/my")：效果：直接响应，没有经过Spring的拦截器

@WebFilter(urlPatterns={"/css/\*","/images/\*"})

@WebListener



推荐可以这种方式；



#### 8.2 使用RegistrationBean

`ServletRegistrationBean`

`FilterRegistrationBean`

`ServletListenerRegistrationBean`

```
@Configuration
public class MyRegistConfig {

    @Bean
    public ServletRegistrationBean myServlet(){
        MyServlet myServlet = new MyServlet();
        return new ServletRegistrationBean(myServlet,"/my","/my02");
    }


    @Bean
    public FilterRegistrationBean myFilter(){
        MyFilter myFilter = new MyFilter();
//        return new FilterRegistrationBean(myFilter,myServlet());
        FilterRegistrationBean filterRegistrationBean = new FilterRegistrationBean(myFilter);
        filterRegistrationBean.setUrlPatterns(Arrays.asList("/my","/css/*"));
        return filterRegistrationBean;
    }

    @Bean
    public ServletListenerRegistrationBean myListener(){
        MySwervletContextListener mySwervletContextListener = new MySwervletContextListener();
        return new ServletListenerRegistrationBean(mySwervletContextListener);
    }
}
```



> 自己创建的`Servlet`和`DispatcherServlet`是同一级，而`DispatcherServlet`的映射路径是`\`，自己创建的`Servlet`的映射路径是`\xxx`，当有一个映射同时满足这两个Servlet时，会优先选择更加匹配的，就是自己写的`Servlet`，这时候就不会触发MVC里的拦截器






### 9. 嵌入式 Servlet 容器

Springboot默认嵌入三种服务器：`Tomcat`, `Jetty`,  `Undertow`

会根据导入的jar包选择服务器，默认导入的`org.springframework.boot`中`spring-boot-starter-tomcat`，所以在springboot的web项目中默认是tomcat服务器。

可以取消导入tomcat的依赖，添加其他服务器的依赖，springboot会自动识别出服务器，自动切换。

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
    <exclusions>
        <!--排除tomcat的依赖-->
        <exclusion>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </exclusion>
    </exclusions>
</dependency>

<!--导入Undertow的依赖-->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-undertow</artifactId>
    <version>3.3.4</version>
</dependency>
```





### 10. 定制化开发

Web应用 编写一个配置类实现 WebMvcConfigurer 即可定制化web功能；+ @Bean给容器中再扩展一些组件。





## 六. 数据访问

### 1. 原生jdbc访问

#### 1.1  导入jdbc场景

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-jdbc</artifactId>
</dependency>
```



#### 1.2  导入mysql驱动

```xml
<!-- MySQL驱动 -->
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
    <version>8.0.28</version>
</dependency>
```



#### 1.3 测试

```java
package com.chr.springboot;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.jdbc.core.JdbcTemplate;

@SpringBootTest
class ApplicationTests {
    @Autowired
    JdbcTemplate jdbcTemplate;

    @Test
    void test() {
        Long l = jdbcTemplate.queryForObject("select COUNT(*) from `table`", Long.class);
        System.out.println(l);
    }
}
```



### 2. 使用Druid连接池

在springboot项目中，默认使用HikariCP，自己可以修改，如使用Druid

```xml
<dependency>
  <groupId>com.zaxxer</groupId>
  <artifactId>HikariCP</artifactId>
  <version>5.1.0</version>
  <scope>compile</scope>
</dependency>
```





#### 2.1 导入druid依赖

```xml
<!--druid整合springboot-->
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid-spring-boot-starter</artifactId>
    <version>1.2.19</version>
</dependency>
```



#### 2.2 修改配置文件

```yaml
spring:
  datasource: # 数据库相关配置
    url: jdbc:mysql://localhost:3306/SpringBoot # 设置数据库url
    username: root # 设置数据库账号
    password: 123 # 设计数据库密码
    driver-class-name: com.mysql.cj.jdbc.Driver # 设置数据库驱动

    druid: 
      aop-patterns: com.chr.springboot.*  #监控SpringBean
      filters: stat,wall     # 底层开启功能，stat（sql监控），wall（防火墙）
      
      stat-view-servlet: # 配置监控页功能
        enabled: true
        login-username: admin
        login-password: admin
        resetEnable: false
      
      web-stat-filter: # 监控web
        enabled: true
        urlPattern: /*
        exclusions: '*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*'
```



**相关配置说明**

[SpringBoot配置示例](https://github.com/alibaba/druid/tree/master/druid-spring-boot-starter)

[配置项列表](https://github.com/alibaba/druid/wiki/DruidDataSource配置属性列表)





### 3. 整合MyBatis

```xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>3.0.3</version>
</dependency>
```



```yaml
mybatis:
  config-location: # 配置文件
  mapper-locations: /mapper/* # mapper xml文件位置
  check-config-location: false # 指定是否对配置文件进行检查
```

<font color="red">配置文件和yaml配置只能选择一种</font>





```java
package com.chr.springboot.javaWeb;

import org.apache.ibatis.annotations.Mapper;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Component;

/**
 * @Author: 程浩然
 * @Create: 2024/11/11 - 19:49
 * @Description: mapper接口
 */

@Mapper
public interface tableMapper {
    @Select("SELECT COUNT(*) FROM `table` WHERE name = #{name}")
    Integer getCount(@Param("name") String name);

}
```

<font color="red">还可以创建mapper.xml文件建立连接，建议采用这种方法。</font>



```java
/**
 * MyBatis
 */
@Autowired
tableMapper tableMapper;

@Test
void MybatisTest() {
    System.out.println(tableMapper.getCount("name1"));
}
```





### 4. 整合MyBatis-plus

● MybatisPlusAutoConfiguration 配置类，MybatisPlusProperties 配置项绑定。mybatis-plus：xxx 就是对mybatis-plus的定制

 ● SqlSessionFactory 自动配置好。底层是容器中默认的数据源

 ● mapperLocations 自动配置好的。有默认值。classpath*:/mapper/**/*.xml；任意包的类路径下的所有mapper文件夹下任意路径下的所有xml都是sql映射文件。  建议以后sql映射文件，放在 mapper下 

● 容器中也自动配置好了 SqlSessionTemplate 

● @Mapper 标注的接口也会被自动扫描；建议直接 @MapperScan("com.atguigu.admin.mapper") 批量扫描就行







## 七. 单元测试

用法：

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-test</artifactId>
    <scope>test</scope>
</dependency>
```



在测试类上加上`@SpringBootTest`注解，在测试方法上加上`@Test`注解

```java
package com.chr.springboot;

@SpringBootTest
public class dataTest {
    @Test
    void MybatisTest() {

    }
}
```





### 1. 注解

| 注解             | 描述                                                         |
| ---------------- | ------------------------------------------------------------ |
| @Test            | 放在方法上，表示这个方法为测试方法                           |
| @DisplayName("") | 可以放在类或方法上，为类或方法起别名                         |
| @BeforeEach      | 放在方法上，**每个**测试方法执行**前**执行一次               |
| @AfterEach       | 放在方法上，**每个**测试方法执行**后**执行一次               |
| @BeforeAll       | 放在方法上，**所有**测试方法执行**前**执行一次（方法必须是static的） |
| @AfterAll        | 放在方法上，**所有**测试方法执行**后**执行一次（方法必须是static的） |
| @Disabled        | 放在方法上，表示这个方法禁用了，不执行                       |
| @Timeout(500)    | 放在方法上，表示这个方法执行多少毫秒后就超时报错             |
| @RepeatedTest(5) | 放在方法上，表示这个测试方法重复5次                          |

```java
package com.chr.springboot;


import org.junit.jupiter.api.*;


@DisplayName("junit5测试")
public class junitTest {
    @Test
    @DisplayName("DisplayName测试")
    void test1() {
        System.out.println(1);
    }

//    @Timeout(value = 1)
    @Disabled
    @Test
    void test2() throws InterruptedException {
        Thread.sleep(1000);
        System.out.println(2);
    }

    @RepeatedTest(5)
    @Test
    void test3() {
        System.out.println(3);
    }

    @BeforeEach
    void beForEach() {
        System.out.println("每个测试前执行一次");
    }

    @AfterEach
    void afterEach() {
        System.out.println("每个测试后执行一次");
    }

    @BeforeAll
    static void beforeAll() {
        System.out.println("所有测试前执行一次");
    }

    @AfterAll
    static void afterAll() {
        System.out.println("所有测试前执行一次");
    }
}

```



### 2. 断言

#### 2.1 简单断言

| 方法            | 说明                                 |
| --------------- | ------------------------------------ |
| assertEquals    | 判断两个对象或两个原始类型是否相等   |
| assertNotEquals | 判断两个对象或两个原始类型是否不相等 |
| assertSame      | 判断两个对象引用是否指向同一个对象   |
| assertNotSame   | 判断两个对象引用是否指向不同的对象   |
| assertTrue      | 判断给定的布尔值是否为 true          |
| assertFalse     | 判断给定的布尔值是否为 false         |
| assertNull      | 判断给定的对象引用是否为 null        |
| assertNotNull   | 判断给定的对象引用是否不为 null      |

```java
@Test
@DisplayName("简单断言")
void assertionsTest1() {
    int a = 1;
    int b = 1;
    assertEquals(a, b);
    assertNotEquals(a, b, "业务逻辑错误");
    ArrayList list = null;
    assertNull(list);
    assertNotNull(list);
    assertTrue(true);
}
```



#### 2.2 数组断言

`assertArrayEquals`，比较两个数组的内容是否相等

```java
@Test
@DisplayName("数组断言")
void assertionsTest2() {
    int[] arr = null;
    int[] brr = new int[]{1, 2, 3};
    assertArrayEquals(arr, brr, "数组值不相等");
}
```



#### 2.3 组合断言

`assertAll`方法接受多个 org.junit.jupiter.api.Executable 函数式接口的实例作为要验证的断言，可以通过 lambda 表达式很容易的提供这些断言

```java
@Test
@DisplayName("assert all")
public void all() {
 assertAll("Math",
    () -> assertEquals(2, 1 + 1),
    () -> assertTrue(1 > 0)
 );
}
```

必须都成功才算成功



#### 2.4 异常断言

`assertThrows`断言，如果存在规定的异常不报错，不存在规定的异常报错

```java
@Test
@DisplayName("异常断言")
void assertionsTest3() {
    assertThrows(ArithmeticException.class, () -> {
        int i = 10 / 1;
    }, "不存在 ArithmeticException 异常");
}
```



#### 2.5 超时断言

`assertTimeout`断言，如果超时了报错，没超时不报错

```java
@Test
@DisplayName("超时断言")
void assertionsTest4() {
    assertTimeout(Duration.ofMillis(10), () -> {
        int i = 1;
        Thread.sleep(100);
    }, "超时");
}
```



#### 2.6 快速失败

通过 `fail `方法直接使得测试失败

```java
@Test
@DisplayName("快速失败")
public void assertionsTest5() {
    fail("失败");
}
```





### 3. 前置条件

JUnit 5 中的前置条件（`assumptions`）类似于断言，不同之处在于不满足的断言会使得测试方法失败，而不满足的前置条件只会使得测试方法的执行终止。前置条件可以看成是测试方法执行的前提，当该前提不满足时，就没有继续执行的必要。

```java
@DisplayName("前置条件")
public class AssumptionsTest {
    private final String environment = "DEV";

    @Test
    @DisplayName("simple")
    public void simpleAssume() {
        assumeTrue(Objects.equals(this.environment, "DEV"));
        assumeFalse(() -> Objects.equals(this.environment, "PROD"));
    }

    @Test
    @DisplayName("assume then do")
    public void assumeThenDo() {
        assumingThat(
                Objects.equals(this.environment, "DEV"),
                () -> System.out.println("In DEV")
        );
    }
}
```

assumeTrue 和 assumFalse 确保给定的条件为 true 或 false，不满足条件会使得测试执行终止。assumingThat 的参数是表示条件的布尔值和对应的 Executable 接口的实现对象。只有条件满足时，Executable 对象才会被执行；当条件不满足时，测试执行并不会终止。





### 4. 参数化测试

使用多种参数来进行一个参数测试

| 注解               | 解释                                                         |
| ------------------ | ------------------------------------------------------------ |
| @ParameterizedTest | 加在方法上，表示这个测试方法是多参数测试，加上后就不用写@Test |
| @ValueSource       | 为参数化测试指定入参来源，支持八大基础类以及String类型,Class类型 |
| @NullSource        | 表示为参数化测试提供一个null的入参                           |
| @EnumSource        | 表示为参数化测试提供一个枚举入参                             |
| @CsvFileSource     | 表示读取指定CSV文件内容作为参数化测试入参                    |
| @MethodSource      | 表示读取指定方法的返回值作为参数化测试入参(注意方法返回需要是一个流) |

```java
@DisplayName("参数化测试")
public class parameterizedTest {
    @ParameterizedTest
    @DisplayName("数值插入")
    @ValueSource(ints = {1, 2, 3, 4, 5})
    void test1(int i) {
        System.out.println(i);
    }
}
```





## 八. 指标监控











## 九.原理解析
