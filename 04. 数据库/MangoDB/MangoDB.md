## 1. 基本介绍

### 适用情况

mangDB适用于`三高`场景：

- High performance - 对数据库高并发读写的需求

- Huge Storage - 对海量数据的高效率存储和访问的需求

- High Scalability && High Availability- 对数据库的高可扩展性和高可用性的需求



对于处理<font color="red">数据量大；IO频繁；价值较低，对事务性要求不高</font>的场景使用

例如：

1. 社交场景，使用 MongoDB 存储存储用户信息，以及用户发表的朋友圈信息，通过地理位置索引实现附近的人、地点等功能。 
2. 游戏场景，使用 MongoDB 存储游戏用户信息，用户的装备、积分等直接以内嵌文档的形式存储，方便查询、高效率存储和访问。 
3. 物流场景，使用 MongoDB 存储订单信息，订单状态在运送过程中会不断更新，以 MongoDB 内嵌数组的形式来存储，一次查询就能将 订单所有的变更读取出来。 
4. 物联网场景，使用 MongoDB 存储所有接入的智能设备信息，以及设备汇报的日志信息，并对这些信息进行多维度的分析。 
5. 视频直播，使用 MongoDB 存储用户信息、点赞互动信息等。



### 体系结构

![MangoDB和MySQL对比](https://ChengHaoRan666.github.io/picx-images-hosting/MangoDB/MangoDB和MySQL对比.8adjaad3tj.webp)



| SQL术语/概念 | MongoDB术语/概念 | 解释/说明                              |
| :----------- | :--------------- | :------------------------------------- |
| database     | database         | 数据库                                 |
| table        | collection       | 数据库表/集合                          |
| row          | document         | 数据记录行/文档                        |
| column       | field            | 数据字段/域                            |
| index        | index            | 索引                                   |
| table joins  | ——               | 表连接，MongoDB不支持                  |
| ——           | 嵌入文档         | MongoDB通过嵌入式文档来替代多表连接    |
| primary key  | primary key      | 主键，MongoDB自动将`_id`字段设置为主键 |



### 数据模型

1. MongoDB的最小存储单位就是文档(document)对象。文档(document)对象对应于关系型数据库的行。
2. 数据在MongoDB中以 BSON（Binary-JSON）文档的格式存储在磁盘上。 BSON（Binary Serialized Document Format）是一种类json的一种二进制形式的存储格式，简称Binary JSON。BSON和JSON一样，支持 内嵌的文档对象和数组对象，但是BSON有JSON没有的一些数据类型，如Date和BinData类型。 
3. BSON采用了类似于 C 语言结构体的名称、对表示方法，支持内嵌的文档对象和数组对象，具有轻量性、可遍历性、高效性的三个特点，可 以有效描述非结构化数据和结构化数据。这种格式的优点是灵活性高，但它的缺点是空间利用率不是很理想。 
4. Bson中，除了基本的JSON类型：string,integer,boolean,double,null,array和object，mongo还使用了特殊的数据类型。这些类型包括 date,object id,binary data,regular expression 和code。每一个驱动都以特定语言的方式实现了这些类型，查看你的驱动的文档来获取详细信息。



| 数据类型        | 描述                                                         | 示例                                             |
| --------------- | ------------------------------------------------------------ | ------------------------------------------------ |
| 字符串          | UTF-8字符串都可表示为字符串类型的数据                        | `{"x": "foobar"}`                                |
| 对象id          | 对象id是文档的12字节的唯一 ID                                | `{"X": ObjectId()}`                              |
| 布尔值          | 真或者假：true 或者 false                                    | `{"x": true}`                                    |
| 数组            | 值的集合或数组表示为成数组                                   | `{"x": ["a", "b", "c"]}`                         |
| 32位整数        | 类型不可用。JavaScript仅支持64位浮点数，所以32位整数会被自动转换 | shell不支持该类型，shell中默认会转换成64位浮点数 |
| 64位整数        | 不支持这个类型。shell会使用一个特殊的内嵌文档类型来显示64位整数 | shell不支持该类型，shell中默认会转换成64位浮点数 |
| 64位浮点数      | shell中的数字就是这一种类型                                  | `{"x": 3.14159 , "y": 3}`                        |
| null            | 表示空值或者未定义的对象                                     | `{"x": null}`                                    |
| undefined       | 文档中也可以使用未定义类型                                   | `{"x": undefined}`                               |
| 符号            | shell不支持，shell会将数据库中的符号类型的数据自动转换成字符串 |                                                  |
| 正则表达式      | 文档中可以包含正则表达式，采用JavaScript的正则表达式语法     | `{"x": /foobar/i}`                               |
| 代码            | 文档中也可以包含JavaScript代码                               | `{"x": function() { /* …… */ }}`                 |
| 二进制数据      | 二进制数据可以以任意字节的串组成，not支持shell中无法使用     |                                                  |
| 最大值 / 最小值 | BSON包括一个特殊类型，表示可能的最大值。shell中没有这个类型  |                                                  |





### 使用特点

（1）高性能：

MongoDB 提供高性能的数据持久性。特别是：

- 对嵌入数据模型的支持减少了数据库系统上的 I/O 活动。
- 索引支持更快的查询，并且可以包含各种自定义文档和数组的键。
  - （文本索引解决搜索的需求，TTL 索引解决历史数据自动过期的需求，地理位置索引可用于构建各种 O2O 应用）

常见存储引擎：`mmapv1`、`wiredtiger`、`mongorocks（rocksdb）`、`in-memory` 等多引擎支持满足各种场景需求。

文件存储方案：`Gridfs` 解决文件存储的需求。



（2）高可用性：

MongoDB 的复制工具称为副本集（replica set），它可提供自动故障转移和数据冗余。



（3）高扩展性：

MongoDB 提供了水平可扩展性作为其核心功能的一部分。

- 分片将数据分布在一组集群的机器上。（海量数据存储，服务能力水平扩展）
- 从 3.4 开始，MongoDB 支持基于片键创建数据区域。在一个平衡的集群中，MongoDB 将一个区域所覆盖的读写只定向到该区域内的那些片。



（4）丰富的查询支持：

MongoDB 支持丰富的查询语言，支持读写操作（CRUD），比如数据聚合、文本搜索和地理空间查询等。



（5）其他特点：

- 如无模式（动态模式）
- 灵活的文档模型



## 2. 常用命令

1. `use 数据库名称` 使用或创建数据库
2. `show dbs` 查看数据库
3. `db` 查看当前正在使用的数据库
4. `db.dropDatabase()` 删除数据库，主要用于删除已经持久化的数据库



5. `db.createCollection(name)` 集合的显式创建
6. `show collections`或`show tables` 查看集合
7. `db.集合名.drop()`删除集合



8. `db.集合名.insertOne({_id:1,name:"张三",age:18})` 插入一条数据
9. `db.集合名.insertMany([{_id:2,name:"李四"},{name:"王五"}])` 插入多条数据
10. `db.table1.deleteOne({name:"张三"})`删除一条数据
11. `db.table1.deleteMany({age:{$lt:18}})`删除符合条件的多条数据
11. `db.collection.updateOne(<filter>, <update>, <options>)` 修改单个文档
11. `db.collection.updateMany(<filter>, <update>, <options>)` 修改多个文档
11. `db.collection.replaceOne(<filter>, <replacement>, <options>)` 替换整个文档
12. `db.table1.find({age :{$gt: 18}},{age:1,_id:0})`查询数据，只显示age
12. `db.table1.count({_id:1})`统计满足条件的数量
12. `db.collection.find().skip(0).limit(10)`查找第一页，每页10条



> 在创建数据库的时候，使用use创建新的数据库的时候，是在内存中创建的，没有在硬盘中，这时候使用`show dbs`查看数据库是没法找到新创建的数据库的。只有在数据库中插入新的集合的时候才会在硬盘持久化，才可以查看

> 默认的数据库有三个：admin，config，local
>
> | 数据库       | 主要用途场景                      | 是否复制                                    | 类比                                                 |
> | :----------- | :-------------------------------- | :------------------------------------------ | :--------------------------------------------------- |
> | **`admin`**  | 权限管理、集群管理命令            | **是**（在副本集中会被复制）                | **公司的HR部门**：管理所有员工的权限和身份。         |
> | **`local`**  | 存储单个节点的特定数据（如oplog） | **否**                                      | **员工的私人备忘录**：只对自己有用，不公开给团队。   |
> | **`config`** | 存储分片集群的元数据和配置        | **是**（作为副本集运行，称为Config Server） | **项目的总架构图**：所有工人都需要根据它来协同工作。 |



> 插入数据时可以指定_id的值，如果不指定，会生成随机值

> mongoDB不要求每个数据的格式一样，一个集合的多条数据的字段可能不同

> 在插入时指定不存在的集合名，会自己创建这个集合，这个就是隐式创建



> 在按照_id删除数据时，要用ObjectId方法包装id
>
> `db.table1.deleteOne({_id:ObjectId("68a93006064b8c6f8dd02b84")});`



> 更新时要使用更新操作符，否则会将内容进行替换
>
> `db.table1.updateOne({_id:1},{age:188})`会删除原本的内容，只保留age：188
>
> 修改操作符：
>
> | 操作符   | 作用                 | 示例                                                         |
> | -------- | -------------------- | ------------------------------------------------------------ |
> | `$set`   | 设置字段值           | db.users.updateOne(   { name: "张三" },   { $set: { age: 26 } } |
> | `$inc`   | 增加/减少数值        | db.users.updateOne(   { name: "张三" },   { $inc: { age: 1 } } ) |
> | `$push`  | 向数组中添加元素     | db.users.updateOne(   { name: "张三" },   { $push: { hobbies: "摄影" } } ) |
> | `$each`  | 向数组中添加多个元素 | db.users.updateOne(   { name: "张三" },   { $push: { hobbies: { $each: ["摄影", "旅行"] } } } ) |
> | `$pull`  | 从数组删除元素       | db.users.updateOne(   { name: "张三" },   { $pull: { hobbies: "游戏" } } ) |
> | `$unset` | 删除字段             | db.users.updateOne(   { name: "张三" },   { $unset: { phone: "" } } ) |
>
> 



## 3. 索引

1. `db.table1.getIndexes()` 查看索引
2. `db.table1.createIndex()`创建索引
   `db.collection.createIndex({ field: 1 })`单字段索引
   `db.collection.createIndex({f1:1, f2:-1})`复合索引
3. `db.table1.dropIndex({_id:1})`删除索引
4. `db.table1.dropIndexes()`删除所以索引
5. `db.table1.find({name:"张三"}).explain()`在后面加上explain可以判断索引是否有用





## 4. 副本集



## 5. 分片集群



## 6. 安全认证
