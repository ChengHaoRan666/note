## 	一.  MyBatis 搭建

##### 1. 创建Maven项目，引入依赖

```xml
<dependencies>
   		<!-- 日志 -->
        <dependency>
            <groupId>ch.qos.logback</groupId>
            <artifactId>logback-classic</artifactId>
            <version>1.4.12</version>
        </dependency>

        <dependency>
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>1.18.24</version>
            <scope>compile</scope>
        </dependency>

        <!--Mybatis-->
        <dependency>
            <groupId>org.mybatis</groupId>
            <artifactId>mybatis</artifactId>
            <version>3.5.16</version>
        </dependency>
    
    <!-- junit测试 -->
    <dependency>
        <groupId>junit</groupId>
        <artifactId>junit</artifactId>
        <version>4.13.1</version>
        <scope>test</scope>
    </dependency>
    
    <!-- MySQL驱动 -->
    <dependency>
            <groupId>mysql</groupId>
            <artifactId>mysql-connector-java</artifactId>
            <version>8.0.28</version>
        </dependency>
</dependencies>
```



##### 2. 创建MyBatis的配置文件 

```xml
<!-- 
mybatis-config.xml 
src/main/resources/mybatis-config.xml
-->

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
        PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
    <!--配置连接数据库的环境-->
    <environments default="development">
        <environment id="development">
            <transactionManager type="JDBC"/>
            <dataSource type="POOLED">
                <property name="driver" value="com.mysql.jdbc.Driver"/>
                <property name="url" value="jdbc:mysql://localhost:3306/mysql_text_one"/>
                <property name="username" value="root"/>
                <property name="password" value="123"/>
            </dataSource>
        </environment>
    </environments>

    <!--引入映射文件-->
    <mappers>
        <mapper resource="org/mybatis/example/BlogMapper.xml"/>
    </mappers>
</configuration>
```



> 其中driver，url，username，password可以放在Properties文件中，使用
> `<properties resource="jdbc.properties"/>`引入



##### 3. 创建表

##### 4. 创建表对应的POJO

```java
package com.chr.MyBatis.POJO;

import lombok.Data;

@Data
public class user {
    private Integer id;
    private String account;
    private String password;
    private String nickname;

    public user() {
    }

    public user(Integer id, String account, String password, String nickname) {
        this.id = id;
        this.account = account;
        this.password = password;
        this.nickname = nickname;
    }
}
```



##### 5. 创建对应 mapper 接口

```java
package com.chr.MyBatis.mapper;

import com.chr.MyBatis.POJO.user;

import java.util.List;

public interface UserMapper {
    
    int insertUser();

    int delete();

    int update();
    
    List<user> select();
}

```



##### 6. 创建MyBatis的映射文件

> 1、映射文件的命名规则：
>
> 表所对应的实体类的类名+Mapper.xml
>
> 例如：表t_user，映射的实体类为User，所对应的映射文件为UserMapper.xml
>
> 
>
> 因此一个映射文件对应一个实体类，对应一张表的操作
>
> MyBatis映射文件用于编写SQL，访问以及操作表中的数据
>
> MyBatis映射文件存放的位置是src/main/resources/mappers目录下

> 2、MyBatis中可以面向接口操作数据，要保证两个一致：
>
> ​		a. <font color="red">mapper接口的全类名和映射文件的命名空间（namespace）保持一致</font>
>
> ​		b. <font color="red">mapper接口中方法的方法名和映射文件中编写SQL的标签的id属性保持一致</font>



```xml
<!-- 
UserMapper.xml 
src/main/resources/mappers/UserMapper.xml
-->

<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="mapper接口的全类名">
    <!--增删改查-->
    <insert id="insertUser">
        insert into t_user values(1,"a1","b1","c1");
    </insert>
</mapper>
```



##### 7. 在配置文件中引入映射信息

```xml
<!--引入映射文件-->
<mappers>
    <mapper resource="mappers/UserMapper.xml"/>
</mappers>
```



##### 8.1 测试：

```java
package com.chr.MyBatis;

import com.chr.MyBatis.mapper.UserMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

/**
 * @Author: 程浩然
 * @Create: 2024/9/5 - 13:48
 * @Description: 入门测试
 */
public class test {
    @Test
    public void UserAddTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession
        SqlSession sqlSession = sqlSessionFactory.openSession();
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现添加 sql 语句
        int result = mapper.insertUser();
        System.out.println(result);
        // 提交事务
        sqlSession.commit();
    }
}
```



##### 8.2 测试优化：

1. 导入日志jar包

2. 写日志配置信息

 ```xml
   <?xml version="1.0" encoding="UTF-8" ?>
   <!DOCTYPE log4j:configuration SYSTEM "log4j.dtd">
   <log4j:configuration xmlns:log4j="http://jakarta.apache.org/log4j/">
       <appender name="STDOUT" class="org.apache.log4j.ConsoleAppender">
           <param name="Encoding" value="UTF-8" />
           <layout class="org.apache.log4j.PatternLayout">
               <param name="ConversionPattern" value="%-5p %d{MM-dd HH:mm:ss,SSS}
   %m (%F:%L) \n" />
           </layout>
       </appender>
       <logger name="java.sql">
           <level value="debug" />
       </logger>
       <logger name="org.apache.ibatis">
           <level value="info" />
       </logger>
       <root>
           <level value="debug" />
           <appender-ref ref="STDOUT" />
       </root>
   </log4j:configuration>
 ```

3. 设置自动提交事务

   ```java
   package com.chr.MyBatis;
   
   import com.chr.MyBatis.mapper.UserMapper;
   import org.apache.ibatis.io.Resources;
   import org.apache.ibatis.session.SqlSession;
   import org.apache.ibatis.session.SqlSessionFactory;
   import org.apache.ibatis.session.SqlSessionFactoryBuilder;
   import org.junit.Test;
   
   import java.io.IOException;
   import java.io.InputStream;
   
   /**
    * @Author: 程浩然
    * @Create: 2024/9/5 - 13:48
    * @Description: 入门测试
    */
   public class test {
       @Test
       public void UserAddTest() throws IOException {
           // 加载核心配置文件
           InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
           // 获取 SqlSessionFactoryBuilder
           SqlSessionFactoryBuilder sqlSessionFactoryBuilder=new SqlSessionFactoryBuilder();
           // 获取 SqlSessionFactory
           SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
           // 获取 SqlSession,设置自动提交事务
           SqlSession sqlSession = sqlSessionFactory.openSession(true);
           // 获取接口对象
           UserMapper mapper = sqlSession.getMapper(UserMapper.class);
           // 实现添加 sql 语句
           int result = mapper.insertUser();
           System.out.println(result);
       }
   }
   ```

##### 8.3 进一步测试--增删改查：

```java
// 测试类
package com.chr.MyBatis;

import com.chr.MyBatis.POJO.user;
import com.chr.MyBatis.mapper.UserMapper;
import org.apache.ibatis.io.Resources;
import org.apache.ibatis.session.SqlSession;
import org.apache.ibatis.session.SqlSessionFactory;
import org.apache.ibatis.session.SqlSessionFactoryBuilder;
import org.junit.Test;

import java.io.IOException;
import java.io.InputStream;

/**
 * @Author: 程浩然
 * @Create: 2024/9/5 - 13:48
 * @Description: 入门测试
 */
public class test {


    @Test
    public void UserInsertTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现添加 sql 语句
        System.out.println(mapper.insertUser());
    }

    @Test
    public void UserDeleteTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现删除 sql 语句
        System.out.println(mapper.delete());
    }

    @Test
    public void UserUpDateTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现修改 sql 语句
        System.out.println(mapper.update());
    }

    @Test
    public void UserSelectTest() throws IOException {
        // 加载核心配置文件
        InputStream is = Resources.getResourceAsStream("mybatis-config.xml");
        // 获取 SqlSessionFactoryBuilder
        SqlSessionFactoryBuilder sqlSessionFactoryBuilder = new SqlSessionFactoryBuilder();
        // 获取 SqlSessionFactory
        SqlSessionFactory sqlSessionFactory = sqlSessionFactoryBuilder.build(is);
        // 获取 SqlSession,设置自动提交事务
        SqlSession sqlSession = sqlSessionFactory.openSession(true);
        // 获取接口对象
        UserMapper mapper = sqlSession.getMapper(UserMapper.class);
        // 实现查询 sql 语句
        for (user user : mapper.select())
            System.out.println(user);
    }
}
```

```xml
<!--UserMapper.xml-->
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "https://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.chr.MyBatis.mapper.UserMapper">
    <!--增加-->
    <insert id="insertUser">
        insert into t_user values(2,"a2","b2","c2");
    </insert>

    <!--删除-->
    <delete id="delete">
        delete from t_user where id = 1;
    </delete>
    <!--修改-->
    <update id="update">
        update t_user set account = '啊', nickname='d1' where id = 2;
    </update>
    <!--查询-->
    <select id="select" resultType="com.chr.MyBatis.POJO.user">
    select * from t_user;
    </select>
</mapper>
```

```java
// UserMapper 接口
package com.chr.MyBatis.mapper;

import com.chr.MyBatis.POJO.user;

import java.util.List;

/**
 * @Author: 程浩然
 * @Create: 2024/9/5 - 13:17
 * @Description: UserMapper 接口
 */
public interface UserMapper {
    /**
     * 增
     */
    int insertUser();

    /**
     * 删
     */
    int delete();


    /**
     * 改
     */
    int update();

    /**
     * 查
     */
    List<user> select();
}
```

进行查询时必须设置 `resultType` 或 `resultMap` 来让MyBatis知道返回结果类型

`resultType`：设置默认的映射关系（要求类的属性名和表的字段名一致）

`resultMap`： 设置自定义的映射关系（可能有多对一或一对多的情况）



## 二. 核心配置文件详解

核心配置文件中的标签必须按照固定的顺序：

properties?,settings?,typeAliases?,typeHandlers?,objectFactory?,objectWrapperFactory?,reflectorF

actory?,plugins?,environments?,databaseIdProvider?,mappers?

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//MyBatis.org//DTD Config 3.0//EN"
"http://MyBatis.org/dtd/MyBatis-3-config.dtd">
<configuration>
    
<!--引入properties文件，此时就可以${属性名}的方式访问属性值-->
<properties resource="jdbc.properties"></properties>

<settings>
<!--将表中字段的下划线自动转换为驼峰-->
<setting name="mapUnderscoreToCamelCase" value="true"/>
    
<!--开启延迟加载-->
<setting name="lazyLoadingEnabled" value="true"/>
</settings>

    
<typeAliases>
<!--
typeAlias：设置某个具体的类型的别名
属性：
type：需要设置别名的类型的全类名
alias：设置此类型的别名，若不设置此属性，该类型拥有默认的别名，即类名且不区分大小写
	   若设置此属性，此时该类型的别名只能使用alias所设置的值
-->

<!--<typeAlias type="com.atguigu.mybatis.bean.User"></typeAlias>-->

<!--<typeAlias type="com.atguigu.mybatis.bean.User" alias="abc">

</typeAlias>-->

<!--以包为单位，设置改包下所有的类型都拥有默认的别名，即类名且不区分大小写-->
<package name="com.atguigu.mybatis.bean"/>
</typeAliases>

<!--
environments：设置多个连接数据库的环境
属性：
default：设置默认使用的环境的id
-->

<environments default="mysql_test">
<!--
environment：设置具体的连接数据库的环境信息
属性：id：设置环境的唯一标识，可通过environments标签中的default设置某一个环境的id，表示默认使用的环境

-->
<environment id="mysql_test">
<!--

transactionManager：设置事务管理方式
属性：type：设置事务管理方式，type="JDBC|MANAGED"
type="JDBC"：设置当前环境的事务管理都必须手动处理
type="MANAGED"：设置事务被管理，例如spring中的AOP

-->
<transactionManager type="JDBC"/>

    
    
<!--

dataSource：设置数据源

属性：
type：设置数据源的类型，type="POOLED|UNPOOLED|JNDI"
type="POOLED"：使用数据库连接池，即会将创建的连接进行缓存，下次使用可以从缓存中直接获取，不需要重新创建

type="UNPOOLED"：不使用数据库连接池，即每次使用连接都需要重新创建
type="JNDI"：调用上下文中的数据源

-->

<dataSource type="POOLED">

<!--设置驱动类的全类名-->

<property name="driver" value="${jdbc.driver}"/>

<!--设置连接数据库的连接地址-->

<property name="url" value="${jdbc.url}"/>

<!--设置连接数据库的用户名-->

<property name="username" value="${jdbc.username}"/>

<!--设置连接数据库的密码-->

<property name="password" value="${jdbc.password}"/>

</dataSource>

</environment>

</environments>

    
    
<!--引入映射文件-->

<mappers>

<mapper resource="UserMapper.xml"/>

<!--

以包为单位，将包下所有的映射文件引入核心配置文件

注意：此方式必须保证mapper接口和mapper映射文件必须在相同的包名下

-->

<package name="com.atguigu.mybatis.mapper"/>

</mappers>

</configuration>
```



## 三. MyBatis 获取参数值的多种种方式

### 1. 获取单个参数

```xml
<!--    int delete(Integer id);-->
    <delete id="delete">
        delete from t_user where id = #{id};
    </delete>
```

获取单个参数可以通过`#{xxx}`的方式获取，在其中填上获取的参数名，参数名可以随便写





### 2. 获取多个参数

若mapper接口中的方法参数为多个时，此时MyBatis==会自动将这些参数放在一个map集合==中，以<font color="red">arg0,arg1...</font>为键，以参数为值；以<font color="red">param1,param2...</font>为键，以参数为值；因此只需要通过`#{}`访问map集合的键就可以获取相对应的值

```java
public void insertUserTest() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    System.out.println(mapper.insertUser(3,"a3","b3","c3"));
}
```



```xml
<insert id="insertUser">
    insert into t_user values(#{param1},#{param2},#{param3},#{param4});
</insert>
```

```xml
<insert id="insertUser">
    insert into t_user values(#{arg0},#{arg1},#{arg2},#{arg3});
</insert>
```





### 3. 获取 map 集合类型的参数

若mapper接口中的方法需要的参数为多个时，此时可以手动创建map集合，将这些数据放在map中，只需要通过和`#{}`访问==map集合的键==就可以获取相对应的值。

```java
@Test
public void insertUserTest() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    Map<String, String> map = new HashMap<>();
    map.put("id", "4");
    map.put("account", "a4");
    map.put("password", "b4");
    map.put("nickname", "c4");
    System.out.println(mapper.insertUser(map));
}
```

```xml
<insert id="insertUser">
    insert into t_user values(#{id},#{account},#{password},#{nickname});
</insert>
```

```java
int insertUser(Map<String,String> map);
```





### 4. 获取实体类型的参数

若mapper接口中的方法参数为实体类对象时，此时可以使用`#{}`，通过==访问实体类对象中的属性名获取属性值==

```java
int insertUser(user user);
```

```xml
<insert id="insertUser">
    insert into t_user values(#{id},#{account},#{password},#{nickname});
</insert>
```

```java
@Test
public void insertUserTest() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    Map<String, String> map = new HashMap<>();
    user user = new user(5, "a5", "b5", "c5");
    System.out.println(mapper.insertUser(user));
}
```



### 5. 使用@Param获取参数

可以通过@Param注解标识`mapper接口中的方法参数`

此时，会将这些参数放在map集合中，以@Param注解的value属性值为键，以参数为值；

以param1,param2...为键，以参数为值；

只需要通过`#{}`访问map集合的键就可以获取相对应的值

```java
int insertUser(@Param("id") Integer id,
               @Param("account") String account,
               @Param("password") String password,
               @Param("nickname") String nickname
);
```

```xml
<insert id="insertUser">
    insert into t_user values(#{id},#{account},#{password},#{nickname});
</insert>
```

```java
@Test
public void insertUserTest() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    System.out.println(mapper.insertUser(6,"a6","b6","c6"));
}
```



## 四. MyBatis的各种查询功能

### 1. 查询一个实体类对象

```java
/**
  * 根据用户id查询用户信息
  */
user getUserById(@Param("id") Integer id);
```

```xml
<select id="getUserById" resultType="User">
	select * from t_user where id = #{id}
</select>
```



### 2. 查询一个 list 集合

```java
/**
  * 查询所有用户信息
  */
List<user> getUserList();
```

```xml
<select id="getUserList" resultType="User">
	select * from t_user
</select>
```



### 3. 查询单个数据

>在MyBatis中，对于Java中常用的类型都设置了类型别名
>
>例如：`java.lang.Integer`-->`int`|`integer`
>
>例如：`int`-->`_int`|`_integer` 
>
>例如：`Map`-->`map `,` List`-->`list`



```java
/**
 * 查询用户表的总记录数
 */
public int getCount();
```

```xml
<select id="getCount" resultType="_int">
    select count(id) from t_user;
</select>
```

```java
@Test
public void selectCount() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    System.out.println(mapper.getCount());
}
```



### 4. 查询一条数据为 map 集合

```xml
<select id="getMapById" resultType="map">
    select * from t_user where id = #{id};
</select>
```

```java
@Test
public void getMapById() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    System.out.println(mapper.getMapById(2));
}
```

```java
/**
 * 根据id查询信息，返回一个map
 */
public Map<String ,String > getMapById(@Param("id") Integer id);
```



### 5. 查询多条数据为 map 集合

##### 方法一：返回一个List，里面放多个map

```java
/**
 * 查询所有信息，返回一个map
 */
public List<Map<String, Objects>> getMap();
```

```xml
<select id="getMap" resultType="map">
    select * from t_user;
</select>
```

```java
@Test
public void getMap() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    System.out.println(mapper.getMap());
}
```



##### 方法二：使用 @MapKey 注解

```xml
<select id="getMap" resultType="map">
    select * from t_user;
</select>
```

```java
@Test
public void getMap() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    UserMapper mapper = sqlSession.getMapper(UserMapper.class);
    System.out.println(mapper.getMap());
}
```

```java
/**
     * 查询所有信息，返回一个map
     */
    @MapKey("id")
    public Map<String, Objects> getMap();
```

> 使用 MapKey 注解会让设置的 val 值为 key，其他值为 val

```txt
结果：
{
2={password=b2, nickname=c2, id=2, account=a2}, 
3={password=a3, nickname=b3, id=3, account=3}, 
4={password=b4, nickname=c4, id=4, account=a4}, 
5={password=b5, nickname=c5, id=5, account=a5}, 
6={password=b6, nickname=c6, id=6, account=a6}}

```



## 五. 特殊sql的执行

### 1. 模糊查询

```java
@Test
public void vagueSelect() throws IOException {
    SqlSession sqlSession = SqlSessionUtils.getSqlSession();
    SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
    for (user user : mapper.vagueSelect("a"))
        System.out.println(user);
}
```

```xml
<select id="vagueSelect" resultType="user">
select * from t_user where account like "%"#{val}"%";
    </select>
```

```java
/**
 * 模糊查询
 */
List<user> vagueSelect (@Param("val") String val);
```

> 采用 "%"#{val}"%" 的形式拼接



### 2. 批量删除

```java
@Test
    public void deleteMore() throws IOException {
        SqlSession sqlSession = SqlSessionUtils.getSqlSession();
        SQLMapper mapper = sqlSession.getMapper(SQLMapper.class);
        System.out.println(mapper.deleteMore("1,2,3 "));
    }
```

```xml
<!--批量删除-->
<delete id="deleteMore">
    delete from t_user where account in (${ids});
</delete>
```

```java
/**
 * 批量删除
 */
int deleteMore(@Param("ids") String ids);
```



### 3. 添加功能获取自增的主键

```java
/**
 * 添加用户信息
 * useGeneratedKeys: 设置使用自增的主键
 * keyProperty: 因为增删改有统一的返回值是受影响的行数，因此只能将获取的自增的主键放在传输的参数user对象的某个属性中
 */
int insertUser(user user);
```

```xml
<insert id="insertUser" useGeneratedKeys="true" keyProperty="id">
	insert into t_user values(null,#{username},#{password},#{age},#{sex})
</insert>
```



> ${}的本质就是字符串拼接，#{}的本质就是占位符赋值
>
> ${}使用字符串拼接的方式拼接sql，若为字符串类型或日期类型的字段进行赋值时，需要手动加单引号
>
> #{}使用占位符赋值的方式拼接sql，此时为字符串类型或日期类型的字段进行赋值时，可以自动添加单引号



## 六. 自定义映射 resultMap

若字段名和实体类中的属性名不一致，则可以通过resultMap设置自定义映射

若字段名和实体类中的属性名一致时，则可以通过resultType 设置映射



### 1. 字段名和属性名不一致

解决字段名和属性名不一致导致的无法自动赋值的解决方法：

> 1. 对于字段名设置别名使其和属性值一致
> 2. 设置全局 mapUnderscoreToCamelCase 来让字段名和属性名一致
> 3. <font color="red">设置自定义映射 resultMap</font>



```xml
<!--
resultMap：设置自定义映射
属性：
id：表示自定义映射的唯一标识
type：查询的数据要映射的实体类的类型
子标签：
id：设置主键的映射关系
result：设置普通字段的映射关系

association：设置多对一的映射关系
collection：设置一对多的映射关系
属性：
property：设置映射关系中 实体类 中的属性名
column：设置映射关系中 表 中的字段名
-->
<resultMap id="userMap" type="User">
<id property="id" column="id"></id>
<result property="userName" column="user_name"></result>
<result property="password" column="password"></result>
<result property="age" column="age"></result>
<result property="sex" column="sex"></result>
</resultMap>

<!--List<User> testMohu(@Param("mohu") String mohu);-->
<select id="testMohu" resultMap="userMap">
<!--select * from t_user where username like '%${mohu}%'-->
select id,user_name,password,age,sex;
</select>
```



### 2. 多对一映射处理<TODO>

1. 级联查询处理映射关系

   ```xml
   <resultMap id="empDeptMap" type="Emp">
   <id column="eid" property="eid"></id>
   <result column="ename" property="ename"></result>
   <result column="age" property="age"></result>
   <result column="sex" property="sex"></result>
   <result column="did" property="dept.did"></result>
   <result column="dname" property="dept.dname"></result>
   </resultMap>
   <!--Emp getEmpAndDeptByEid(@Param("eid") int eid);-->
   <select id="getEmpAndDeptByEid" resultMap="empDeptMap">
   select emp.*,dept.* from t_emp emp left join t_dept dept on emp.did =
   dept.did where emp.eid = #{eid}
   </select>
   ```

2. 使用  association 处理映射关系

   > property：需要处理多对一的映射关系的属性名
   > javaType：该属性的类型

   ```xml
   <resultMap id="empDeptMap" type="Emp">
   <id column="eid" property="eid"></id>
   <result column="ename" property="ename"></result>
   <result column="age" property="age"></result>
   <result column="sex" property="sex"></result>
   <association property="dept" javaType="Dept">
   <id column="did" property="did"></id>
   <result column="dname" property="dname"></result>
   </association>
   </resultMap>
   <!--Emp getEmpAndDeptByEid(@Param("eid") int eid);-->
   <select id="getEmpAndDeptByEid" resultMap="empDeptMap">
   select emp.*,dept.* from t_emp emp left join t_dept dept on emp.did =
   dept.did where emp.eid = #{eid}
   </select>
   ```

3. 分布查询

    ```xml
    <resultMap id="empDeptStepMap" type="Emp">
    <id column="eid" property="eid"></id>
    <result column="ename" property="ename"></result>
    <result column="age" property="age"></result>
    <result column="sex" property="sex"></result>
    <!--
    select：设置分步查询，查询某个属性的值的sql的标识（namespace.sqlId）
    column：将sql以及查询结果中的某个字段设置为分步查询的条件
    -->
        <association property="dept"
    select="com.atguigu.MyBatis.mapper.DeptMapper.getEmpDeptByStep" column="did">
    </association>
    </resultMap>
    <!--Emp getEmpByStep(@Param("eid") int eid);-->
    <select id="getEmpByStep" resultMap="empDeptStepMap">
    select * from t_emp where eid = #{eid}
    </select>
    ```



> 分步查询的优点：可以实现延迟加载，但是必须在核心配置文件中设置全局配置信息：lazyLoadingEnabled：延迟加载的全局开关。当开启时，所有关联对象都会延迟加载aggressiveLazyLoading：当开启时，任何方法的调用都会加载该对象的所有属性。 否则，每个属性会按需加载此时就可以实现按需加载。`默认关闭`
>
> 获取的数据是什么，就只会执行相应的sql。此时可通过association和collection中的fetchType属性设置当前的分步查询是否使用延迟加载，fetchType="lazy(延迟加载)|eager(立即加载)"



### 3. 一对多映射处理<TODO>

```xml
<resultMap id="deptEmpStep" type="Dept">
<id property="did" column="did"></id>
<result property="dname" column="dname"></result>
<collection property="emps" fetchType="eager"
select="com.atguigu.MyBatis.mapper.EmpMapper.getEmpListByDid" column="did">
</collection>
</resultMap>
<!--Dept getDeptByStep(@Param("did") int did);-->
<select id="getDeptByStep" resultMap="deptEmpStep">
select * from t_dept where did = #{did}
</select>
```

一对多映射用fetchType表名集合中存储的数据

多对一映射用javaType表名属性的类型

## 七. 动态 SQL

Mybatis框架的动态SQL技术是一种根据特定条件动态拼装SQL语句的功能，它存在的意义是为了解决拼接SQL语句字符串时的痛点问题。



### 1. if 标签

if标签可通过test属性的表达式进行判断，若表达式的结果为true，则标签中的内容会执行；反之标签中的内容不会执行

```xml
<!--List<Emp> getEmpListByMoreTJ(Emp emp);-->
<select id="getEmpListByMoreTJ" resultType="Emp">
select * from t_emp where 1=1
<if test="ename != '' and ename != null">
and ename = #{ename}
</if>
<if test="age != '' and age != null">
and age = #{age}
</if>
<if test="sex != '' and sex != null">
and sex = #{sex}
</if>
</select>
```



### 2. where 标签

where 标签动态生成

```xml
<select id="getEmpListByMoreTJ2" resultType="Emp">
select * from t_emp
<where>
<if test="ename != '' and ename != null">
ename = #{ename}
</if>
<if test="age != '' and age != null">
and age = #{age}
</if>
<if test="sex != '' and sex != null">
and sex = #{sex}
</if>
</where>
</select>
```



> where和if一般结合使用：
>
> a > 若where标签中的if条件都不满足，则where标签没有任何功能，即不会添加where关键字
>
> b>若where标签中的if条件满足，则where标签会自动添加where关键字，并将条件最前方多余的and去掉
>
> 注意：where标签不能去掉条件最后多余的and



### 3. trim 标签

```xml
<select id="getEmpListByMoreTJ" resultType="Emp">
select * from t_emp
<trim prefix="where" suffixOverrides="and">
<if test="ename != '' and ename != null">
ename = #{ename} and
</if>
<if test="age != '' and age != null">
age = #{age} and
</if>
<if test="sex != '' and sex != null">
sex = #{sex}
</if>
</trim>
</select>
```



> trim用于去掉或添加标签中的内容
>
> 常用属性：
>
> prefix：在trim标签中的内容的前面添加某些内容
>
> prefixOverrides：在trim标签中的内容的前面去掉某些内容
>
> suffix：在trim标签中的内容的后面添加某些内容
>
> suffixOverrides：在trim标签中的内容的后面去掉某些内容



### 4. choose、when、otherwise 标签

相当于 Switch....  case....... default

```xml
<select id="getEmpListByChoose" resultType="Emp">
select * from t_emp
<where>
<choose>
<when test="ename != '' and ename != null">
ename = #{ename}
</when>
<when test="age != '' and age != null">
age = #{age}
</when>
<when test="sex != '' and sex != null">
sex = #{sex}
</when>
<when test="email != '' and email != null">
email = #{email}
</when>
</choose>
</where>
</select>
```



### 5. foreach 标签

```xml
<!--int insertMoreEmp(List<Emp> emps);-->
<insert id="insertMoreEmp">
insert into t_emp values
<foreach collection="emps" item="emp" separator=",">
(null,#{emp.ename},#{emp.age},#{emp.sex},#{emp.email},null)
</foreach>
</insert>


<!--int deleteMoreByArray(int[] eids);-->
<delete id="deleteMoreByArray">
delete from t_emp where
<foreach collection="eids" item="eid" separator="or">
eid = #{eid}
</foreach>
</delete>


<!--int deleteMoreByArray(int[] eids);-->
<delete id="deleteMoreByArray">
delete from t_emp where eid in
<foreach collection="eids" item="eid" separator="," open="(" close=")">
#{eid}
</foreach>
</delete>
```

>属性：
>
>collection：设置要循环的数组或集合
>
>item：表示集合或数组中的每一个数据
>
>separator：设置循环体之间的分隔符
>
>open：设置foreach标签中的内容的开始符
>
>close：设置foreach标签中的内容的结束符





#### 6. include 标签

sql片段，可以记录一段公共sql片段，在使用的地方通过include标签进行引入，方便代码复用

```xml
<sql id="empColumns">eid,ename,age,sex,did</sql>
select <include refid="empColumns"></include> from t_emp
```





## 八. Mybatis 的缓存

<font color="red">缓存只对查询操作有效</font>

### 1.Mybatis 的一级缓存

==一级缓存是SqlSession级别的==，通过同一个SqlSession查询的数据会被缓存，下次查询相同的数据，就会从缓存中直接获取，不会从数据库重新访问。

使一级缓存失效的四种情况：

1) 不同的SqlSession对应不同的一级缓存

2) 同一个SqlSession但是查询条件不同

3) 同一个SqlSession两次查询期间执行了任何一次增删改操作

4) 同一个SqlSession两次查询期间手动清空了缓存

> 一级缓存是默认开启的



### 2.Mybatis 的二级缓存

二级缓存是==SqlSessionFactory级别==，通过同一个 SqlSessionFactory 创建的 SqlSession 查询的结果会被缓存；此后若再次执行相同的查询语句，结果就会从缓存中获取

二级缓存开启的条件：

1. 在核心配置文件中，设置全局配置属性cacheEnabled="true"，默认为true，不需要设置

2. 在映射文件中设置标签`<cache />`

3. 二级缓存必须在SqlSession关闭或提交之后有效

4. 查询的数据所转换的实体类类型必须实现序列化的接口(`implements Serializable`)



使二级缓存失效的情况：

两次查询之间执行了任意的增删改，会使一级和二级缓存同时失效



### 3. 二级缓存的相关配置

在mapper配置文件中添加的`cache`标签可以设置一些属性：

- eviction属性：缓存回收策略
  1. LRU（Least Recently Used） – 最近最少使用的：移除最长时间不被使用的对象。
  2. FIFO（First in First out） – 先进先出：按对象进入缓存的顺序来移除它们。
  3. SOFT – 软引用：移除基于垃圾回收器状态和软引用规则的对象。
  4. WEAK – 弱引用：更积极地移除基于垃圾收集器状态和弱引用规则的对象。

> eviction 属性默认的是 LRU

- flushInterval属性：刷新间隔，单位毫秒

> 默认情况是不设置，也就是没有刷新间隔，缓存仅仅调用语句时刷新

- size属性：引用数目，正整数

> 代表缓存最多可以存储多少个对象，太大容易导致内存溢出

- readOnly属性：只读，true/false
  1. true：只读缓存；会给所有调用者返回缓存对象的相同实例。因此这些对象不能被修改。这提供了很重要的性能优势。
  2. false：读写缓存；会返回缓存对象的拷贝（通过序列化）。这会慢一些，但是安全，因此==默认是false==。



### 4. 缓存查询的顺序

1. 先查询二级缓存，因为二级缓存中可能会有其他程序已经查出来的数据，可以拿来直接使用。
2. 如果二级缓存没有命中，再查询一级缓存
3. 如果一级缓存也没有命中，则查询数据库
4. SqlSession 关闭之后，一级缓存中的数据会写入二级缓存





## 九. 分页插件

添加分页插件依赖

```xml
<!--分页插件-->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper</artifactId>
    <version>5.3.1</version>
</dependency>
```



配置类中设置分页插件

```java
// 设置分页插件
sqlSessionFactoryBean.setPlugins(new PageInterceptor());
```



代码：

```java
public String pageTest() {
    PageHelper.startPage(1, 20); // 开启分页功能
    List<seller> sellers = sellerDao.selectSellerAll();
    sellers.forEach(System.out::println);
    return "index";
}
```



得到的返回值为分页设置后的结果



> 1. 在查询功能之前使用PageHelper.startPage(int pageNum, int pageSize)开启分页功能pageNum：当前页的页码
>    pageSize：每页显示的条数

> 2. 在查询获取list集合之后，使用PageInfo<T> pageInfo = new PageInfo<>(List<T> list, int navigatePages)获取分页相关数据
>
>    list：分页之后的数据
>    navigatePages：导航分页的页码数



> 分页相关数据：
>
> PageInfo{
>
> pageNum=8, pageSize=4, size=2, startRow=29, endRow=30, total=30, pages=8,
>
> list=Page{count=true, pageNum=8, pageSize=4, startRow=28, endRow=32, total=30,
>
> pages=8, reasonable=false, pageSizeZero=false},
>
> prePage=7, nextPage=0, isFirstPage=false, isLastPage=true, hasPreviousPage=true,
>
> hasNextPage=false, navigatePages=5, navigateFirstPage4, navigateLastPage8,
>
> navigatepageNums=[4, 5, 6, 7, 8]
>
> }



> 常用数据：
>
> pageNum：当前页的页码
>
> pageSize：每页显示的条数
>
> size：当前页显示的真实条数
>
> total：总记录数
>
> pages：总页数
>
> prePage：上一页的页码
>
> nextPage：下一页的页码
>
> isFirstPage/isLastPage：是否为第一页/最后一页
>
> hasPreviousPage/hasNextPage：是否存在上一页/下一页
>
> navigatePages：导航分页的页码数
>
> navigatepageNums：导航分页的页码，[1,2,3,4,5]