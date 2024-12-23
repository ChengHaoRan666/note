## Statement SQL连接

使用 Statement 可以实现静态SQL连接（没有动态值语句）

<font color="blue">DriverManager  ->  Connection  ->  Statement  ->  ResultSet</font>



### 步骤：

第一步：导入jar包，设置依赖。(只做一次)

第二步：注册驱动。

第三步：获取连接。

第四步：获取执行者对象。

第五步：执行sql语句，并接收返回结果。

第六步：处理结果。

第七步：释放资源。

```java
import com.mysql.cj.jdbc.Driver;
import java.sql.*;

public class StatementQueryPart {
    public static void main(String[] args) throws Exception , ClassNotFoundException{
        //2.注册驱动
        // DriverManager.registerDriver(new Driver());
        Class.forName("com.mysql.cj.jdbc.Driver");


        //3.获取连接 （连接的数据库名是db2，第二个第三个参数是连接数据库的用户名密码）
        Connection connection = DriverManager.getConnection("jdbc:mysql://127.0.0.1:3306/mysql_text_one", "root", "164578");

        //4.获取执行者对象
        Statement statement = connection.createStatement();

        //5.执行sql语句，并且接收结果
        String sql = "SELECT * FROM account;";
        //execute执行，query：查询，resultset：结果集
        ResultSet resultSet = statement.executeQuery(sql);

        //6.处理结果
        while (resultSet.next()) {
            System.out.println(resultSet.getString("name") + "\t" + resultSet.getString("money"));
        }

        //7.释放资源
        connection.close();
        statement.close();
        connection.close();
    }
}
```



### 步骤详细讲解：

##### 第一步：导入jar包设置依赖

需要在官网上下载对应的jar包，在idea中设置依赖。



##### 第二步：注册驱动

```java
// 方法一
DriverManager.registerDriver(new Driver());

// 方法二
new Driver();

// 方法三
Class.forName("com.mysql.cj.jdbc.Driver");
```

> 方法三优于方法二，方法二优于方法一

在方法一中，registerDriver方法注册一次驱动，在参数中（即 new Driver())存在静态代码块：

```java
static {
    try {
        DriverManager.registerDriver(new Driver());
    } catch (SQLException var1) {
        throw new RuntimeException("Can't register driver!");
    }
}
```

也会注册一次驱动。也就是说，方法一注册两次驱动。



==静态代码块在类的加载时被执行，只会执行一次。==

以下方式可以触发类加载：

1. new 关键字
2. 调用静态方法
3. 调用静态属性
4. 反射
5. 子类触发父类
6. 程序入口main



为解决这个问题，我们在两次注册中选择一个，优化出方法二。

但是方法二写死了。进一步用反射优化出方法三。

其中字符串可以在配置文件中设置，不用改代码。



##### 第三步：获取连接

通过DriverManager.getConnection() 方法进行获取连接，返回 Connection 对象。

```java
// DriverManager.getConnection()方法有三种重载方式。
DriverManager.getConnection(String url);
DriverManager.getConnection(String url, Properties info);
DriverManager.getConnection(String url, String user, String password);
```

三个参数的格式：

url：jdbc:数据库管理软件名称://ip地址:port端口号/数据库名？key=value
user：用户名
password：密码

两个参数的格式：

url：jdbc:数据库管理软件名称://ip地址:port端口号/数据库名？key=value
info：一个Properties 对象，存用户名，密码。

一个参数格式：

url：jdbc:数据库管理软件名称://ip地址:port端口号/数据库名?user=root&password=root&key=val



##### 第四步：获取执行者对象

```java
// Statement 可以发送sql语句到数据库，并且获取返回结果
Statement statement = connection.createStatement();
```



##### 第五步：发送sql语句，并接收返回结果

```java
String sql = "select * from account;";
// 两种方式发送
// 方法一
int i = statement.executeUpdate(sql);
// 方法二
ResultSet resultset = statement.executeQuery(sql);
```

> SQL分类：
>
> DDL(容器创建，修改，删除语句)
> DML(插入，修改，删除语句)
> DQL(查询语句)
> DCL(权限控制语句)
> TCL(事务控制语句)

executeUpdate 可以执行 <font color="red">非DQL语句</font>。

如果执行DML语句，返回影响的行数。

如果执行非DML语句，返回0。



executeQuery 执行<font color="red">DQL语句</font>。

返回结果封装对象 resultset。



##### 第六步：处理结果

返回的 resultset 有一个指针，指向当前行数据。初始指向 -1，有一个next() 方法，向后移动指针。如果下一个不为空，返回T，为空返回F。

获取列的数据有两种方法：按照字段名获取，按照下标获取。

==注意：按照下标获取从 1 开始==

```java
while(resultset.next()){
    System.out.println(resultset.get类型(String 列名（别名）| int col));
}
```



##### 第七步：释放资源

```java
connection.close();
statement.close();
connection.close();
```





### 缺点：

1. sql语句需要自己编写，较为复杂。
2. 存在注入攻击问题。
