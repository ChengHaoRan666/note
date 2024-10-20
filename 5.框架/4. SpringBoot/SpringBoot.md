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

> 方式二：通过 @Bean ，@Component，@Service，@Controller，@Repository注解
>
> @Bean是加在方法上，将返回值作为容器交给Spring管理

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



















## 六. 数据访问

## 七. 单元测试

## 八. 指标监控

## 九.原理解析
