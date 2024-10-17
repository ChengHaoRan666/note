## Spring 介绍

1. Spring 是轻量级的开源的 JavaEE 框架
2. Spring 可以解决企业应用开发的复杂性
3. Spring 有两个核心部分：IOC 和 AOP
   1. IOC：控制反转，把创建对象过程交给 Spring 进行管理
   2. AOP：面向切面，不修改源代码进行功能增强
4. Spring 特点
   1. 方便解耦，简化开发
   2. Aop 编程支持
   3. 方便程序测试
   4. 方便和其他框架进行整合
   5. 方便进行事务操作
   6. 降低 API 开发难度

![](https://ChengHaoRan666.github.io/picx-images-hosting/66bf3c94d9c307b7e9a62952.3k7xmiakmu.webp)



运行最基本 Spring 需要导入 jar 包：

- commons-logging-1.1.1.jar
- spring-beans-5.2.6.RELEASE.jar
- spring-context-5.2.6.RELEASE.jar
- spring-core-5.2.6.RELEASE.jar
- spring-expression-5.2.6.RELEASE.jar



IOC 注解需要导入 jar 包：

- spring-aop-5.2.6.RELEASE.jar



AOP 需要导入 jar 包：

- com.springsource.net.sf.cglib-2.2.0.jar

- com.springsource.org.aopalliance-1.0.0.jar

- com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar

- spring-aop-5.2.6.RELEASE.jar

- spring-aspects-5.2.6.RELEASE.jar





## IOC 

### 1. 什么是 IOC：

1. 控制反转，把对象创建和对象之间的调用过程，交给 Spring 进行管理
2. 使用 IOC 目的：为了降低耦合度



### 2. IOC 底层原理：

1. xml 解析
2. 工厂模式：service 通过工厂模式调用 Dao 
3. 反射

![](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image-20240805153434812.4ckt493mq1.webp)

 

### 3. IOC 容器实现两种方式：（两个接口）

> IOC 思想基于 IOC 容器完成，IOC 容器底层就是对象工厂

- `BeanFactory`：IOC 容器基本实现，是 Spring 内部的使用接口，不提供开发人员进行使用。==加载配置文件时候不会创建对象，在获取对象（使用）才去创建对象==

- `ApplicationContext`：BeanFactory 接口的子接口，提供更多更强大的功能，一般由开发人员进行使用。==加载配置文件时候就会把在配置文件对象进行创建==

实现`ApplicationContext`接口的有两个类：`FileSystemXmlApplicationContext` 和`ClassPathXmlApplicationContext`。

`FileSystemXmlApplicationContext`类中参数是从盘符开始

`ClassPathXmlApplicationContext`类中参数是从工程src下开始



### 4. IOC 操作 Bean 管理

#### 4.1 什么是 Bean 管理

1. Spring 创建对象
2. Spring 注入属性



#### 4.2 Bean 管理操作有两种方式

1. 基于 xml 配置文件方式实现
2. 基于注解方式实现



#### 4.3 基于 xml 配置文件操作 Bean 管理

##### 4.3.1 基于 xml 配置文件创建对象

在 Spring 配置文件中，使用 bean 标签，标签里面添加对应属性，就可以实现对象创建。

```xml
<!--id：唯一标识-->
<!--class：全类名-->
<bean id="user" class="com.chr.Spring5.ioc.User"></bean>
```

> 创建对象时，默认执行的是无参的构造方法

```java
// 读取配置文件
ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean1.xml");
// 创建 bean 实例
book book = applicationContext.getBean("user", User.class);
```



##### 4.3.2 基于 xml 配置文件注入属性

> DI：依赖注入，就是注入属性

<font color="red">**方式一：在配置文件中写，使用set方法注入属性**</font>

```xml
<bean id="book" class="com.chr.Spring5.ioc.book">
    <property name="name" value="1"></property>
</bean>
```

```java
@Test
    public void test() {
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean1.xml");
        book book = applicationContext.getBean("book", book.class);
        System.out.println(book.getName());
    }
// 创建的book实例就会注入属性为 1，要求book要提供set方法
```



<font color="red">**方式二： 在配置文件中写，使用有参数构造进行注入属性**</font>

```xml
<bean id="book" class="com.chr.Spring5.ioc.book">
    <!--构造方法中的参数值-->
    <constructor-arg name="name" value="张三"></constructor-arg>
    <constructor-arg name="age" value="18"></constructor-arg>
</bean>
```

```java
@Test
public void test2() {
    ApplicationContext applicationContext = new ClassPathXmlApplicationContext("bean1.xml");
    book book = applicationContext.getBean("book", book.class);
    System.out.println(book.getName() + " " + book.getAge());
}
```





xml 注入特殊类型属性方式：

1. 字面值

```xml
<!--设置null--> <property name="address"><null/></property>

<!--设置特殊符号-->
<property name="address">
 <value><![CDATA[<<南京>>]]></value>
</property>
```

2. 外部 bean：通过ref 将外部bean注入，需要在userService中有一个userDao对象用于赋值

```xml
<!--service 和 dao 对象创建-->
<bean id="userService" class="com.chr.spring5.service.UserService">
 <!--注入 userDao 对象
 name 属性：类里面属性名称
 ref 属性：创建 userDao 对象 bean 标签 id 值
 -->
 <property name="userDao" ref="userDaoImpl"></property>
</bean>
<bean id="userDaoImpl" class="com.chr.spring5.dao.UserDaoImpl"></bean>
```

3. 内部 bean：

```xml
<!--内部 bean-->
<bean id="emp" class="com.chr.spring5.bean.Emp">
 <!--设置两个普通属性-->
 <property name="ename" value="lucy"></property>
 <property name="gender" value="女"></property>
 <!--设置对象类型属性-->
 <property name="dept">
     <!--嵌套一个内部 bean-->
 <bean id="dept" class="com.chr.spring5.bean.Dept">
 <property name="dname" value="安保部"></property>
 </bean>
     
 </property>
</bean>
```

4. 集合属性：set注入，构造器注入都行

```xml
<!--集合注入-->
<bean id="stu" class="com.chr.Spring5.ioc.collectiontype.stu">
    <property name="courses">
        <array>
            <value>1</value>
            <value>2</value>
            <value>3</value>
            <value>4</value>
        </array>
    </property>
    <property name="list">
        <list>
            <value>11</value>
            <value>22</value>
            <value>33</value>
            <value>44</value>
        </list>
    </property>
    <property name="map">
        <map>
            <entry key="key1" value="111"></entry>
            <entry key="key2" value="222"></entry>
            <entry key="key3" value="333"></entry>
            <entry key="key4" value="444"></entry>
        </map>
    </property>
</bean>
```

```java
public class stu {
    private String[] courses;
    private List<String> list;
    private Map<String, String> map;

    public String[] getCourses() { }
    public void setCourses(String[] courses) {}
    public List<String> getList() {}
    public void setList(List<String> list) {}
    public Map<String, String> getMap() {}
    public void setMap(Map<String, String> map) {}
    @Override
    public String toString() {}
}
```

```java
public class stuTest {
    @Test
    public void test1() {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean2.xml");
        Object stu = context.getBean("stu", stu.class);
        System.out.println(stu);

    }
}
```



##### 4.3.3 两种 bean

1. 普通 bean：在配置文件中定义 bean 类型就是返回类型
2. 工厂 bean：在配置文件中定义 bean 类型可以和返回类型不一样

创建类实现 FactoryBean 即是工厂 bean，可定义返回的 bean 类型

```java
public class myBean implements FactoryBean<User> {

    // 定义返回类型
    @Override
    public User getObject() throws Exception {
        User user = new User();
        return user;
    }

    @Override
    public Class<?> getObjectType() {
        return null;
    }
}
```

```java
public class myBeanTest {
    @Test
    public void test1() {
        ApplicationContext context = new ClassPathXmlApplicationContext("bean3.xml");
        User myBean = context.getBean("myBean", User.class);
        System.out.println(myBean);
    }
}
```



##### 4.3.4 bean 单实例/多实例

> 在 Spring 里面，设置创建 bean 实例是单实例

在 spring 配置文件 bean 标签里面有属性（scope）用于设置单实例还是多实例

scope 属性值第一个值 默认值 singleton，表示是单实例对象

第二个值 prototype，表示是多实例对象



singleton 和 prototype 区别：

1. singleton 单实例，prototype 多实例

2. 设置 scope 值是 singleton 时候，加载 spring 配置文件时候就会创建单实例对象。
   设置 scope 值是 prototype 时候，不是在加载 spring 配置文件时候创建 对象，在调用getBean 方法时候创建多实例对象。



##### 4.3.5 bean 生命周期

> 生命周期是指从对象创建到对象销毁的过程

生命周期：

1. 通过构造器创建 bean 实例（默认无参数构造，若 xml 有配置调用配置）
2. 为 bean 的属性设置值和对其他 bean 的引用（调用 set 方法）
3. 调用 bean 的初始化方法（需要进行配置初始化的方法）
4. bean 可以使用了（对象获取了）
5. 当容器关闭时，调用 bean 的销毁方法（需要进行配置销毁的方法）

> 初始化方法需要在 xml 中用 init-method 指向
>
> 销毁方法需要在 xml 文件中用 destroy-method 指向，还要用 close 方法销毁

```java
package com.chr.Spring5.ioc.bean;

/**
 * @Author: 程浩然
 * @Create: 2024/8/5 - 22:23
 * @Description: 生命周期演示
 */
public class Orders {
    private String name;

    public Orders() {
        System.out.println("1 执行无参构造");
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
        System.out.println("2 执行set方法进行配置");
    }

    /**
     * 创建初始化 init 方法
     */
    public void init() {
        System.out.println("3 初始化");
    }

    /**
     * 销毁方法
     */
    public void dest() {
        System.out.println("5 销毁对象");
    }
}
```

```java
package com.chr.Spring5.ioc.bean;

import org.junit.Test;
import org.springframework.context.support.ClassPathXmlApplicationContext;

import static org.junit.Assert.*;

/**
 * @Author: 程浩然
 * @Create: 2024/8/5 - 22:27
 * @Description: bean 生命周期演示
 */
public class OrdersTest {
    @Test
    public void test1() {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("bean4.xml");
        Orders orders = context.getBean("Orders", Orders.class);
        System.out.println("4 实例化");
        context.close();
    }
}
```

```xml
<bean id="Orders" class="com.chr.Spring5.ioc.bean.Orders" init-method="init" destroy-method="dest">
    <property name="name" value="手机"></property>
</bean>
```

![](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image-20240805231323384.7zqcrrzf7u.webp)

生命周期（加入后置处理器版本）：

1. 通过构造器创建 bean 实例（默认无参数构造，若 xml 有配置调用配置）
2. 为 bean 的属性设置值和对其他 bean 的引用（调用 set 方法）
3. 把 bean 实例传递 bean 后置处理器的方法 postProcessBeforeInitialization
4. 调用 bean 的初始化方法（需要进行配置初始化的方法）
5. 把 bean 实例传递 bean 后置处理器的方法 postProcessAfterInitialization
6. bean 可以使用了（对象获取了）
7. 当容器关闭时，调用 bean 的销毁方法（需要进行配置销毁的方法）

演示：先创建类实现接口 BeanPostProcessor，即为后置处理器。

```java
package com.chr.Spring5.ioc.bean;

import org.springframework.beans.BeansException;
import org.springframework.beans.factory.config.BeanPostProcessor;

/**
 * @Author: 程浩然
 * @Create: 2024/8/5 - 23:08
 * @Description: 后置处理器
 */
public class beanPostProcessor implements BeanPostProcessor {
    @Override
    public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("3 传入 postProcessBeforeInitialization 方法");
        return bean;
    }

    @Override
    public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
        System.out.println("5 传入 postProcessAfterInitialization 方法");
        return bean;
    }
}

```

在xml中配置 beanPostProcessor 类

```xml
<bean id="beanPostProcessor" class="com.chr.Spring5.ioc.bean.beanPostProcessor"></bean>
```

![](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/image-20240805231424600.8z6g4y26dm.webp)



##### 4.3.6 xml 自动装配

自动装配是指根据指定装配规则（属性名称或者属性类型），Spring 自动将匹配的属性值进行注入。

```xml
<!--实现自动装配
 bean 标签属性 autowire，配置自动装配
 autowire 属性常用两个值：
 byName 根据属性名称注入 ，注入值 bean 的 id 值和类属性名称一样
 byType 根据属性类型注入
-->
<bean id="emp" class="com.atguigu.spring5.autowire.Emp" autowire="byName"></bean>
```



#### 4.4 基于 注解 操作 Bean 管理

添加 spring-aop-5.2.6.RELEASE.jar 用于注解，在配置文件中开启组件扫描

```xml
<context:component-scan base-package="com.chr.Spring5.ioc.annotate"/>
```

- 注解是代码特殊标记，格式：@注解名称(属性名称=属性值, 属性名称=属性值..)
- 使用注解，注解作用在类上面，方法上面，属性上面
- 使用注解目的：简化 xml 配置



##### 4.4.1 基于 注解 操作创建对象

- ==@Component==：用于普通创建对象

- ==@Service==：用于 service 层

- ==@Controller==用于 web 层

- ==@Repository==：用于 dao 层

四个注解都可以用来创建 bean 实例

步骤：

1. 创建类
2. 在类上面添加对象注解（注解里面的 value 属性值可以省略不写，默认值是类名（首字母变为小写））
3. 创建成功

```java
// 注解类，value 属性省略，默认为类名首字母小写
@Component
public class Annotate {

}
```

```java
// 注解测试类
public class AnnotateTest {
    @Test
    public void test1(){
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("/com/chr/Spring5/ioc/Annotate/bean1.xml");
        Annotate annotate = context.getBean("annotate", Annotate.class);
        System.out.println(annotate.getClass().getName());
    }
}
```

```xml
<!-- xml配置文件，开启组件扫描 -->
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd
                           http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context.xsd
">
<context:component-scan base-package="com.chr.Spring5.ioc.annotate"/>
</beans>
```



##### 4.4.2 注解 操作创建对象时组件扫描细节

```xml
<context:component-scan base-package="com.chr.Spring5.ioc.annotate"/>
```

使用`<context:component-scan>`标签来开启组件扫描

相关参数：

1. base-package（必填）：设置要扫描的基准包

2. use-default-filters：取值true，false。表示是否使用默认过滤器，不使用需要自己配置。

   1. ```xml
      <!--自己配置过滤器，设置根据注解筛选，注解为 expression 的值-->
      <context:include-filter type="annotation" expression="org.springframework.stereotype.Component"/>
      ```

   2. ```xml
      <!--自己配置过滤器，根据注解筛选，注解不为 expression 的值-->
      <context:exclude-filter type="annotation" expression="org.springframework.stereotype.Component"/>
      ```

3. resource-pattern：限定扫描的资源，比如只扫描特定后缀的类文件。

   

##### 4.4.3 基于 注解 操作注入属性

- ==@Autowired==：根据属性类型进行注入
- ==@Qualifier==：根据名称注入，要和==@Autowired==一起使用
- ==@Resource==：根据属性类型或名称进行注入

- ==@Value==：注入普通类型属性



==@Autowired==根据属性类型自动装配：

```java
@Service
public class UserService {
    // Autowired 根据类型进行自动注入
    @Autowired
    private UserDao userDao;

    public void show() {
        System.out.println("Service add......");
        userDao.show();
    }
}
```

```java
@Repository
public class UserDao {
    public void show() {
        System.out.println("userDao add......");
    }
}
```

```java
public class UserServiceTest {
    @Test
    public void test1() {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("/com/chr/Spring5/ioc/annotate/bean1.xml");
        UserService userService = context.getBean("userService", UserService.class);
        userService.show();
    }
}
```



==@Qualifier==根据名称自动注入：

> 要和 ==@Autowired== 一起使用，进行更具体筛选

```java
@Service
public class UserService {
	// 用于解决一个接口有多个实现类的情况，使用名称进行注入
    @Autowired
    @Qualifier(value = "userDao")
    private UserDao userDao;

    public void show() {
        System.out.println("Service add......");
        userDao.show();
    }
}
```



==@Resource==根据类型 或 名称 进行注入

```java
@Service
public class UserService {
    @Resource // 根据类型注入
    // @Resource(name = "userDao") // 根据名称注入
    private UserDao userDao;

    public void show() {
        System.out.println("Service add......");
        userDao.show();
    }
}
```



==@Value== 注入普通类型属性

```java
public class UserService {
    @Value(value = Math.PI+"")
    public double ab;
}
```



##### 4.4.2 完全注解开发

```java
@Configuration //作为配置类，替代 xml 配置文件
// 开启组件扫描
@ComponentScan(basePackages = {"com.chr.Spring5.ioc.annotate"})

public class* SpringConfig {

}
```

```java
@Test
public void test2() {
 //加载配置类时改变了，使用 AnnotationConfigApplicationContext 开发
 ApplicationContext context
 = new AnnotationConfigApplicationContext(SpringConfig.class);
 UserService userService = context.getBean("userService", 
UserService.class);
 System.out.println(userService);
 userService.add();
}
```







## AOP

### 1. 概念 及 原理，目的

**概念**：   面向切面编程（方面），利用 AOP 可以对业务逻辑的各个部分进行隔离，从而使得

业务逻辑各部分之间的耦合度降低，提高程序的可重用性，同时提高了开发的效率。

**原理**：使用<font color="red">**动态代理**</font>

第一种情况：有接口情况，使用 JDK 动态代理

创建接口实现类代理对象，增强类的方法。



第二种情况：无接口情况，使用 CGLIB 动态代理

创建子类的代理方法，增强类的方法。



**目的**：在不改变源代码的情况下添加新功能。



### 2. AOP  操作用语

1. ==连接点==：类里面那些方法可以被增强，这些方法称为连接点
2. ==切入点==：实际被增强的方法，称为切入点
3. ==通知(增强)==：实际增强的逻辑部分称为通知(增强)，
   通知有多种类型：
   a. 前置通知 ：通知在切入点之前执行
   b. 后置通知 ：通知在切入点之后执行
   c. 环绕通知 ：通知在切入点之前之后都执行
   d. 异常通知 ：通知在切入点出现异常时执行
   e. 最终通知 ：无论有没有异常都执行
4. ==切面==：切面是动作，是指把通知应用到切入点的过程



### 3. AOP 操作

> Spring 框架一般都是基于 AspectJ 实现 AOP 操作

#### 1. 准备工作

导入jar包：

- ```java
  com.springsource.net.sf.cglib-2.2.0.jar
  ```

- ```java
  com.springsource.org.aopalliance-1.0.0.jar
  ```

- ```java
  com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar
  ```

- ```java
  spring-aop-5.2.6.RELEASE.jar
  ```

- ```java
  spring-aspects-5.2.6.RELEASE.jar
  ```

切入点表达式：

作用：知道对于那个类中的那个方法进行增强

结构：`execution([权限修饰符][返回类型][类全路径][方法名称]([参数列表]))`				

<font color="orange">返回类型可以省略不写</font>

> 举例 1：对 com.atguigu.dao.BookDao 类里面的 add 进行增强
>
> `execution(* com.atguigu.dao.BookDao.add(..))`

> 举例 2：对 com.atguigu.dao.BookDao 类里面的所有的方法进行增强
>
> `execution(* com.atguigu.dao.BookDao.* (..))`



#### 2. AspectJ 注解

##### 步骤：

1. 创建待增强的类和方法

   ```java
   public class User {
       public void add(){
           System.out.println("add......");
       }
   }
   ```

2. 创建增强类和增强方法

   ```java
   public class UserProxy {
       public void before(){
           System.out.println("before......");
       }
   }
   ```

3. 在配置文件中开启组件扫描

   ```xml
   <context:component-scan base-package="com.chr.Spring5.AOP"/>
   ```

4. 在配置文件中开启生成代理对象

   ```xml
   <aop:aspectj-autoproxy/>
   ```

5. 在带增强类和增强类上加上注解

   ```java
   // 待增强类
   @Component
   public class User {
       public void add(){
           System.out.println("add......");
       }
   }
   ```

   ```java
   // 增强类
   @Component
   @Aspect
   public class UserProxy {
       public void before(){
           System.out.println("before......");
       }
   }
   ```

6. 在增强方法上加上注解

   ```java
   @Component
   @Aspect
   public class UserProxy {
       @Before(value = "execution(* com.chr.Spring5.AOP.User.add())")
       public void before(){
           System.out.println("before......");
       }
   }
   ```

   

在第⑥步时方法的注解有五种，分别对应==前置通知==，==后置通知==，==环绕通知==，==异常通知==，==最终通知==。分别用注解

```java
@Component
@Aspect
public class UserProxy {
    //前置通知
    @Before(value = "execution(* com.chr.Spring5.AOP.User.add())")
    public void before() {
        System.out.println("before.........");
    }
    
    //后置通知（返回通知）
    @AfterReturning(value = "execution(* com.chr.Spring5.AOP.User.add(..))")
    public void afterReturning() {
        System.out.println("afterReturning.........");
    }
    
    //环绕通知
    @Around(value = "execution(* com.chr.Spring5.AOP.User.add(..))")
    public void around(ProceedingJoinPoint proceedingJoinPoint) throws Throwable 	{
        System.out.println("环绕之前.........");
        //被增强的方法执行
        proceedingJoinPoint.proceed();
        System.out.println("环绕之后.........");
    }
    
    //异常通知
    @AfterThrowing(value = "execution(* com.chr.Spring5.AOP.User.add(..))")
    public void afterThrowing() {
        System.out.println("afterThrowing.........");
    }
    
    //最终通知
    @After(value = "execution(* com.chr.Spring5.AOP.User.add(..))")
    public void after() {
        System.out.println("after.........");
    }
}
```



##### 公共切入点提取：

```java
@Pointcut(value = "execution(* com.chr.Spring5.AOP.User.add())")
public void point(){}

// value 的值可以写上面声明的方法名
@Before(value = "point()")
public void before() {
    System.out.println("1 before.........");
}
```



##### 设置增强类优先级：

在类上面添加以下注解，数值越小，优先级越高

```java
@Order(1)
```