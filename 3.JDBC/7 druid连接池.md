## druid连接池

- 数据库连接池负责分配、管理和释放数据库连接
- 它允许应用程序重复使用一个现有的数据库连接，而不是再重新建立一个
- 这项技术能明显提高对数据库操作的性能

> 连接池都实现了javax.sql.DataSource接口。**他是 java 官方提供的数据库连接池规范(接口)**



使用步骤：

1. 导入druid的jar包
2. 硬编码/软编码创建连接池
3. 使用

```java
// 硬编码创建连接池
public void testHard() throws SQLException {
        // 1. 创建一个druid连接池对象
        DruidDataSource dataSource = new DruidDataSource();

        // 2. 设置连接池参数【 必须 | 非必须】
        // 必须：
        dataSource.setUrl("jdbc:mysql://localhost:3306/mysql_text_one");
        dataSource.setUsername("root");
        dataSource.setPassword("123");
        dataSource.setDriverClassName("com.mysql.cj.jdbc.Driver");
        // 非必须：
        dataSource.setInitialSize(5); // 初始化连接数量
        dataSource.setMaxActive(10); // 最大连接数量
        
        // 3. 获取连接【通用方法，所有连接池都一样】
        Connection connection = dataSource.getConnection();
        
        // 中间进行crud
        
        // 4. 回收连接【通用方法，所有连接池都一样】
        connection.close();
    }
```

```java
	// 软编码创建连接池
    // 通过读取外部配置文件的方式实例化druid连接池对象
    public void testSoft() throws Exception {
        // 1. 读取外部配置文件 Properties
        Properties properties = new Properties();
        InputStream ips = DruidUsePart.class.getClassLoader().getResourceAsStream("druid.properties");
        properties.load(ips);

        // 2. 使用连接池的工具类的工程模式，创建连接池
        DataSource dataSource = DruidDataSourceFactory.createDataSource(properties);

        // 3. 创建连接
        Connection connection = dataSource.getConnection();

        // 进行curd

        // 4. 回收
        connection.close();
    }
```

软连接创建一个配置文件 druid.properties ，通过这个配置文件设置druid连接池。

```properties
# druid 连接池的配置文件
# 配置信息类似于key = val
driverClassName=com.mysql.cj.jdbc.Driver
username=root
password=123
url=jdbc:mysql://localhost:3306/mysql_text_one
```
