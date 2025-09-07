## 存储引擎

### 1. Mysql体系结构



![Mysql体系结构](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/Mysql体系结构.2h8nl1bnug.webp)

> 自上而下：连接层，服务层，引擎层，存储层

#### 1. 连接层

最上层是一些客户端和链接服务，包含本地sock 通信和大多数基于客户端/服务端工具实现的类似于 TCP/IP的通信。主要完成一些类似于==连接处理==、==授权认证==、及相关的安全方案。在该层上引入了线程池的概念，为通过认证安全接入的客户端提供线程。同样在该层上可以实现基于SSL的安全链接。服务器也会为安全接入的每个客户端验证它所具有的操作权限。



#### 2. 服务层

第二层架构主要完成大多数的核心服务功能，如SQL接口，并完成缓存的查询，SQL的分析和优化，部分内置函数的执行。所有跨存储引擎的功能也在这一层实现，如过程、函数等。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应的优化如确定表的查询的顺序，是否利用索引等， 最后生成相应的执行操作。如果是select语句，服务器还会查询内部的缓存，如果缓存空间足够大， 这样在解决大量读操作的环境中能够很好的提升系统的性能。



#### 3. 引擎层

存储引擎层，存储引擎真正的负责了MySQL中数据的存储和提取，服务器通过API和存储引擎进行通信。不同的存储引擎具有不同的功能，这样我们可以根据自己的需要，来选取合适的存储引擎。数据库中的索引是在存储引擎层实现的。



#### 4. 存储层

数据存储层， 主要是将数据(如: redolog、undolog、数据、索引、二进制日志、错误日志、查询 日志、慢查询日志等)存储在文件系统之上，并完成与存储引擎的交互。



### 2. 存储引擎介绍

1. 存储引擎就是存储数据、建立索引、更新/查询数据等技术的实现方式
2. 存储引擎是基于表的，而不是 基于库的，所以存储引擎也可被称为表类型
3. 我们可以在创建表的时候指定存储引擎，如果没有指定，使用默认的 InnoDB 存储引擎

> 在建表语句加上`ENGINE =InnoDB`可以指定存储引擎

> 使用`show engines`可以查看支持的存储引擎

| 存储引擎                        | 是否支持 | 注解                                         | 事务 | 分布式事务 | 保存点 |
| ------------------------------- | -------- | -------------------------------------------- | ---- | ---------- | ------ |
| <font color="red">MEMORY</font> | YES      | 基于哈希，存储在内存中，适用于临时表         | NO   | NO         | NO     |
| MRG_MYISAM                      | YES      | 相同的 MyISAM 表集合                         | NO   | NO         | NO     |
| CSV                             | YES      | CSV 存储引擎                                 | NO   | NO         | NO     |
| FEDERATED                       | NO       | 联合 MySQL 存储引擎                          |      |            |        |
| PERFORMANCE_SCHEMA              | YES      | 性能模式                                     | NO   | NO         | NO     |
| <font color="red">MyISAM</font> | YES      | MyISAM 存储引擎                              | NO   | NO         | NO     |
| <font color="red">InnoDB</font> | DEFAULT  | 支持事务、行级锁定和外键                     | YES  | YES        | YES    |
| ndbinfo                         | NO       | MySQL Cluster 系统信息存储引擎               |      |            |        |
| BLACKHOLE                       | YES      | /dev/null 存储引擎（写入的任何内容都会消失） | NO   | NO         | NO     |
| ARCHIVE                         | YES      | 归档存储引擎                                 | NO   | NO         | NO     |
| ndbcluster                      | NO       | 集群容错表                                   |      |            |        |



### 3. 存储引擎特点

#### 1. InnoDB

InnoDB是一种兼顾高可用性和高性能的通用存储引擎，在MySQL 5.5 之后作为默认存储引擎。



##### 特点：

1. 支持事务：DML操作（增删改）遵循ACID模型，支持事务
2. 行级锁：支持行级锁，提高并发访问性能
3. 外键：支持外键 FOREIGN KEY 约束，保证数据完整性和正确性



##### 文件存储：

InnoDB存储引擎会将每张表的表结构，数据，索引存放在 ==表名.ibd==文件中。

> show variables like 'innodb_file_per_table' 
> 参数设置的是是否将每个表的数据，索引单独存放在一个.ibd文件中。5.6.6之后默认开启

> 可以使用`ibd2sdi 表名.ibd`查看表的数据



##### 逻辑存储结构：

![InnoDB逻辑结构](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/InnoDB逻辑结构.3uv6p4jsos.webp)

**Tablespace（表空间）**

- 是 InnoDB 存储引擎管理数据的最顶层逻辑结构。
- 可以是共享系统表空间（如 `ibdata1`），也可以是每个表独立的表空间（`.ibd` 文件）。

**Segment（段）**

- 在表空间内，一个 **segment** 通常和一个具体的索引（如主键或二级索引）对应。
- 可以看作是存储逻辑上的单位，比如一个“数据段”或“索引段”。

**Extent（区）**

- Segment 向操作系统申请空间时，至少以 Extent 为单位分配。
- 每个 Extent 默认大小约为 1 MB（共包含 64 个 16 KB 的页），具体大小可由 `innodb_page_size` 设置决定。

**Page（页）**

- Page 是 InnoDB 的基本存储单位，默认大小为 16 KB，可配置为其他值。
- 每个页包含多个组成部分，包括：**FIL header/trailer（文件头尾）**、**页头（Page header）**、逻辑记录行（Row），以及页面目录（用于快速定位记录）等。

**Row（行）**

- 行记录存储在 Data Page（数据页）中。每条记录封装了多种信息：主键值（聚簇索引关键字）、事务 ID、回滚指针（undo log 定位）、非键列的实际数据等。
- Page 中还包含两个特殊的“哨兵”记录：**infimum**（值最小）和 **supremum**（值最大），用于标记记录的起始和结束位置，并且通过链表方式串接用户记录，支持顺序扫描。





#### 2. MyISAM

MyISAM是MySQL早期的默认存储引擎。



##### 特点：

1. 不支持事务，不支持外键
2. 支持表锁，不支持行锁
3. 访问速度快



##### 文件存储：

1. 表名.sdi：存储表结构信息
2. 表名.MYD：存储数据
3. 表名.MYI：存储索引





#### 3. MEMORY

Memory引擎的表数据是存储在内存中的，由于受到硬件问题，断电问题等影响，只能将这些表作为临时表或缓存使用。



##### 特点：

1. 内存存放
2. hash索引（默认）



##### 文件存储：

表名.sdi：存储表结构信息





#### 4. 总结

| 特点         | InnoDB              | MyISAM | Memory |
| ------------ | ------------------- | ------ | ------ |
| 存储限制     | 64TB                | 有     | 有     |
| 事务安全     | 支持                | -      | -      |
| 锁机制       | 行锁                | 表锁   | 表锁   |
| B+tree 索引  | 支持                | 支持   | 支持   |
| Hash 索引    | -                   | -      | 支持   |
| 全文索引     | 支持 (5.6 版本之后) | 支持   | -      |
| 空间使用     | 高                  | 低     | N/A    |
| 内存使用     | 高                  | 低     | 中等   |
| 批量插入速度 | 低                  | 高     | 高     |
| 支持外键     | 支持                | -      | -      |



### 4. 存储引擎选择

在选择存储引擎时，应该根据应用系统的特点选择合适的存储引擎。对于复杂的应用系统，还可以根据 实际情况选择多种存储引擎进行组合。

- InnoDB: 是Mysql的默认存储引擎，支持事务、外键。如果应用对事务的完整性有比较高的要 求，在并发条件下要求数据的一致性，数据操作除了插入和查询之外，还包含很多的更新、删除操 作，那么InnoDB存储引擎是比较合适的选择。 
- MyISAM ： 如果应用是以读操作和插入操作为主，只有很少的更新和删除操作，并且对事务的完整性、并发性要求不是很高，那么选择这个存储引擎是非常合适的。 
- MEMORY：将所有数据保存在内存中，访问速度快，通常用于临时表及缓存。MEMORY的缺陷就是 对表的大小有限制，太大的表无法缓存在内存中，而且无法保障数据的安全性。



## 索引

索引（index）是帮助MySQL高效获取数据的数据结构（有序）在数据之外，数据库系统还维护着满足特定查找算法的数据结构，这些数据结构以某种方式引用（指向）数据， 这样就可以在这些数据结构 上实现高级查找算法，这种数据结构就是索引



优点：

1. 提高数据检索效率，降低数据库IO成本
2. 通过索引对数据进行排序，降低数据排序成本，降低CPU消耗

缺点：

1. 数据库也需要维护记录索引，会占用一定空间
2. 索引大大提高了查询的效率，同时也降低了更新表的速度，对表进行增删改操作时速度降低



### 1. 索引数据结构

MySQL的索引是在存储引擎层实现的，不同的存储引擎支持的索引不同。索引主要包括：

| 索引结构                            | 描述                                                         |
| ----------------------------------- | ------------------------------------------------------------ |
| <font color="red">B+Tree索引</font> | 最常见的索引类型，大部分引擎都支持 B+ 树索引                 |
| Hash索引                            | 底层数据结构是用哈希表实现的, 只有精确匹配索引列的查询才有效, 不 支持范围查询 |
| R-tree（空间索引）                  | 空间索引是MyISAM引擎的一个特殊索引类型，主要用于地理空间数据类 型，通常使用较少 |
| Full-text（全文索引）               | 是一种通过建立倒排索引,快速匹配文档的方式。类似于 Lucene,Solr,ES |

不同存储引擎对索引数据结构的支持：

| 索引           | InnoDB           | MyISAM | Memory |
| -------------- | ---------------- | ------ | ------ |
| B+tree 索引    | 支持             | 支持   | 支持   |
| Hash 索引      | 不支持           | 不支持 | 支持   |
| R-tree 索引    | 不支持           | 支持   | 不支持 |
| Full-text 索引 | 5.6 版本之后支持 | 支持   | 不支持 |

#### 1. 二叉树

如果使用二叉树，比较理想的情况是：

![二叉树](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/二叉树.8hgtpv7suh.webp)

但是会出现极端情况：

![极端二叉树](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/极端二叉树.8adlufo303.webp)

可见，使用二叉树存在两个问题：

1. 顺序插入时，会形成一个链表，查询效率大大降低
2. 数据过多时，深度过大，检索性能慢



#### 2. 红黑树

解决第一个问题可以改为使用红黑树，红黑树自旋会让树保持平衡，但是第二个问题红黑树还是没法解决

![红黑树](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/红黑树.54y3vhywig.webp)



> 所以在实现索引时，并没有采用二叉树或红黑树



#### 3. b 树

b树是一个多叉路衡查找树，相较于二叉树，b 树每个节点可以有多个分支，即多叉

一个最大度数（一个节点的子节点的数量）为5的 b 树，每个节点最多存储 4 个 key，5 个指针

![b树](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/b树.8hgtpvlj6h.webp)



#### 4. b+ 树

1. 所有数据都存在叶子节点
2. 非叶子节点只存放索引（key）和子指针
3. 叶子节点之间通常用链表相连

![b+树](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/b+树.4uba2d6nsr.webp)



Mysql对于b+树又做了优化：增加一个指向相邻叶子节点 的链表指针，就形成了带有顺序指针的B+Tree，提高区间访问的性能，利于排序

![Mysql优化后b+树](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/Mysql优化后b+树.wiwlp0tnq.webp)



#### 5. Hash

哈希索引就是采用一定的hash算法，将键值换算成新的hash值，映射到对应的槽位上，然后存储在 hash表中。

如果两个(或多个)键值，映射到一个相同的槽位上，他们就产生了hash冲突（也称为hash碰撞），可以通过链表来解决。



特点：

1. Hash索引只能用于对等比较(=，in)，不支持范围查询（between，>，< ，...）
2. 无法利用索引完成排序操作
3. 查询效率高，通常(不存在hash冲突的情况)只需要一次检索就可以了，效率通常要高于B+tree索引



### 2. 索引分类

#### 索引分类

在 MySQL 数据库中，索引的具体类型主要分为以下几类：主键索引、唯一索引、常规索引、全文索引。

| 分类     | 含义                                                 | 特点                     | 关键字   |
| -------- | ---------------------------------------------------- | ------------------------ | -------- |
| 主键索引 | 针对于表中主键创建的索引                             | 默认自动创建，只能有一个 | PRIMARY  |
| 唯一索引 | 避免同一表中某数据列中的值重复                       | 可以有多个               | UNIQUE   |
| 常规索引 | 快速定位特定数据                                     | 可以有多个               |          |
| 全文索引 | 全文索引查找的是文本中的关键词，而不是比较索引中的值 | 可以有多个               | FULLTEXT |



#### 聚集索引&二级索引

而在在InnoDB存储引擎中，根据索引的存储形式，又可以分为以下两种：

| 分类                             | 含义                                                       | 特点                 |
| -------------------------------- | ---------------------------------------------------------- | -------------------- |
| 聚集索引<br /> (Clustered Index) | 将数据存储与索引放到了一块，索引结构的叶子节点保存了行数据 | 必须有，而且只有一个 |
| 二级索引 (Secondary Index)       | 将数据与索引分开存储，索引结构的叶子节点关联的是对应的主键 | 可以存在多个         |

聚集索引选取规则: 

1. 如果存在主键，主键索引就是聚集索引
2. 如果不存在主键，将使用第一个唯一（UNIQUE）索引作为聚集索引
3. 如果表没有主键，或没有合适的唯一索引，则 InnoDB 会自动生成一个 rowid 作为隐藏的聚集索引



聚集索引和二级索引的具体结构如下：

![聚集索引和二级索引](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/聚集索引和二级索引.5fkxoyl0bu.webp)

- <font color="red">聚集索引的叶子节点下挂的是这一行的数据 </font>
- <font color="red">二级索引的叶子节点下挂的是该字段值对应的主键值</font>

> 回表查询： 先到二级索引中查找数据，找到主键值，然后再到聚集索引中根据主键值，获取 数据的方式，就称之为回表查询。





### 3. 索引语法

#### 创建索引
```sql
CREATE [UNIQUE | FULLTEXT] INDEX index_name ON table_name (index_col_name, ...);
```

- UNIQUE：唯一索引，保证列中值不重复
- FULLTEXT：全文索引，用于 `CHAR`、`VARCHAR`、`TEXT` 等字段，支持关键词搜索
- index_name：索引名
- table_name：表名
- index_col_name：要建立索引的字段（可以是多个：联合索引）



#### 查看索引

```sql
SHOW INDEX FROM table_name;
```

| 字段          | 含义                                                     |
| ------------- | -------------------------------------------------------- |
| Table         | 表名                                                     |
| Non_unique    | 是否可以重复。0 表示唯一（不能重复），1 表示可以重复     |
| Key_name      | 索引名称                                                 |
| Seq_in_index  | 索引中列的顺序，从 1 开始                                |
| Column_name   | 列名                                                     |
| Collation     | 索引中列的排序方式，A=升序，D=降序，NULL=未定义          |
| Cardinality   | 索引中唯一值的估计数量（优化器用来估算成本，不一定精确） |
| Sub_part      | 前缀索引的长度。如果是整列索引则为 NULL                  |
| Packed        | 索引是否压缩                                             |
| Null          | 该列是否可以为 NULL                                      |
| Index_type    | 索引类型，一般是 BTREE、FULLTEXT、HASH 等                |
| Comment       | 备注                                                     |
| Index_comment | 索引的额外说明                                           |
| Visible       | MySQL 8.0 引入，YES=可见，NO=不可见                      |



#### 删除索引

```sql
DROP INDEX index_name ON table_name;
```





### 4. SQL性能分析

#### 1. SQL执行频率

通过 `show [session|global] status` 命令可以提供服务器状态信息

通过如下指令，可以查看当前数据库的INSERT、UPDATE、DELETE、SELECT的访问频次： 

```sql
-- session 是查看当前会话;
-- global 是查询全局数据;
SHOW GLOBAL STATUS LIKE 'Com_______';
```

查询结果：

| Variable_name                       | Value | 解释                                                        |
| ----------------------------------- | ----- | ----------------------------------------------------------- |
| Com_binlog                          | 0     | 执行 `BINLOG` 命令的次数（通常内部使用，用户很少直接执行）  |
| Com_commit                          | 0     | `COMMIT` 语句执行次数                                       |
| <font color="red">Com_delete</font> | 0     | `DELETE` 语句执行次数                                       |
| Com_import                          | 0     | `IMPORT TABLE` 命令的次数（极少使用）                       |
| <font color="red">Com_insert</font> | 0     | `INSERT` 语句执行次数                                       |
| Com_repair                          | 0     | `REPAIR TABLE` 命令的次数（主要用于 MyISAM）                |
| Com_revoke                          | 0     | `REVOKE` 权限命令执行次数                                   |
| <font color="red">Com_select</font> | 417   | `SELECT` 查询语句执行次数                                   |
| Com_signal                          | 0     | `SIGNAL` 语句执行次数（主要用于触发器、存储过程的错误处理） |
| <font color="red">Com_update</font> | 0     | `UPDATE` 语句执行次数                                       |
| Com_xa_end                          | 0     | `XA END` 命令执行次数（分布式事务相关）                     |



#### 2. 慢查询日志

慢查询日志记录了所有执行时间超过指定参数（`long_query_time`，单位：秒，默认10秒）的所有 SQL 语句的日志

通过`show variables like 'slow_query_log';`可以查看慢查询日志是否开启

window系统在`C:\ProgramData\MySQL\MySQL Server 8.0`目录下有 my.ini 配置文件

linux系统在`etc/my.cnf`有配置文件

修改配置文件中`slow-query-log=1`即为开启慢查询日志；修改`long_query_time`设置慢查询指定时间



#### 3. profile详情

show profiles 能够在做SQL优化时帮助我们了解时间都耗费到哪里去了

```sql
-- 查看是否支持show profiles
SELECT @@have_profiling;
```

默认profile是关闭的，还需要开启：

```sql
-- 设置当前会话profile功能开启
set session profiling =1;
-- 设置全局profile功能开启
set global profiling =1;
```

可以查看是否开启了profile功能：

```sql
SHOW VARIABLES LIKE 'profiling';
```



可以用命令查看sql执行情况：

```sql
-- 查看每一条SQL的耗时基本情况
show profiles;

-- 查看指定query_id的SQL语句各个阶段的耗时情况
show profile for query query_id;

-- 查看指定query_id的SQL语句CPU的使用情况
show profile cpu for query query_id;
```



#### 4. explain

`EXPLAIN`或者 `DESC` 命令获取 MySQL 如何执行 SELECT 语句的信息，包括在 SELECT 语句执行 过程中表如何连接和连接的顺序

```sql
-- 直接在select语句之前加上关键字 explain / desc
EXPLAIN SELECT 字段列表 FROM 表名 WHERE 条件;
```

例如：

```sql
-- 执行
explain select * from c_class;
```

| 字段          | 含义                                                         |
| ------------- | ------------------------------------------------------------ |
| id            | select 查询的序列号，表示查询中执行 select 子句或者是操作表的顺序。（id 相同，执行顺序从上到下；id 不同，值越大，越先执行） |
| select_type   | 表示 SELECT 的类型，常见取值：<br> - SIMPLE：简单表（不使用表连接或子查询）<br> - PRIMARY：主查询（外层查询）<br> - UNION：UNION 中的第二个或后面的查询语句<br> - SUBQUERY：在 SELECT/WHERE 之后包含子查询 |
| table         | 当前查询所涉及的表或衍生表                                   |
| partitions    | 匹配的分区（仅分区表时显示）                                 |
| type          | 表示连接类型，性能由好到差依次为：<br> ==NULL → system → const → eq_ref → ref → range → index → all== |
| possible_keys | 显示可能应用在这张表上的索引，一个或多个                     |
| key           | 实际使用的索引，如果为 NULL，则表示没有使用索引              |
| key_len       | 表示索引中使用的字节数，该值为索引字段最大可能长度（非实际使用长度）。在不损失精确性的前提下，长度越短越好 |
| ref           | 索引匹配方式/比较对象：<br />const（常量）<br />eq\_ref（唯一索引匹配）<br/>ref（普通索引匹配）<br/>func（函数计算结果）<br/>NULL |
| rows          | MySQL 认为必须要执行查询的行数，在 InnoDB 引擎的表中是估计值，可能并不总是准确 |
| filtered      | 表示返回结果的行数占需读取行数的百分比，值越大越好。         |
| Extra         | 额外信息，说明优化器的选择和操作                             |





### 5. 索引使用原则

#### 1. 最左前缀法则

如果索引了多列（联合索引），要遵守最左前缀法则

最左前缀法则指的是查询从索引的最左列开始， 并且不跳过索引中的列。如果跳跃某一列，索引将会部分失效(后面的字段索引失效)

> 例如创建了联合索引：
> `create index idx_sku_sn on tb_sku (id, sn, name)`
> 对于==id==，==sn==，==name==创建索引
>
> 如果查询语句没有使用id，那么用了sn，name也不能用索引加速
>
> 如果查询语句没有使用sn，使用了id，name，那么只能对id进行加速，sn，name不能加速
>
> 如果查询语句没有使用name，那么只能对id，sn进行加速，name不能加速



通过最左前缀法则可以知道：

1. <font color="red">在创建联合索引的时候尽量按照字段的重要性进行排序，这样可以最大程度的利用多级索引</font>
2. <font color="red">在写查询语句的时候字段的顺序是无所谓的，优化器会统一分析改为索引的顺序执行</font>



#### 2. 范围查询

联合索引中，出现范围查询(>,<)，在查询语句中范围查询右侧的列索引失效，就是<font color="orange">遇到范围查询会截断后面的索引（按照索引创建的顺序）</font>

> 例如创建了联合索引：
> `create index idx_sku_sn on tb_sku (id, sn, name)`
> 对于==id==，==sn==，==name==创建索引
>
> 
>
> 执行`select * from tb_sku where sn = '6' and id > 1 and name = 'name'`
>
> name 和 sn  的索引就会失效

<font color="red">但是使用>=, <= 不会导致索引失效，所以尽量使用>=, <= 来代替>, <</font>



#### 3. 索引失效情况

1. **索引列运算**：在索引列上进行运算操作， 索引将失效
2. **字符串不加引号**：字符串类型字段使用时，不加引号，索引将失效
3. **模糊查询 like**：如果仅仅是尾部模糊匹配（xx%），索引不会失效。如果是头部模糊匹配（%xx），索引失效
4. **or连接条件**：用or分割开的条件， 如果 or 前的条件中的列有索引，而后面的列中没有索引，那么涉及的索引都不会被用到，只有 or 两边的条件中的列都有索引才会生效
5. **数据分布影响**：如果MySQL评估使用索引比全表更慢，则不使用索引



#### 4. SQL提示

SQL在执行时会进行判断走不走，走哪个索引，我们人为也可以进行干涉

- `use index`： 建议 MySQL 使用哪一个索引完成此次查询
-  `ignore index`： 忽略指定的索引
- `force index`： 强制使用索引

```sql
-- use index: 建议使用
explain select * from tb_user use index(idx_user) where profession = '计科';

-- ignore index: 忽略
explain select * from tb_user ignore index(idx_user) where profession = '计科';

-- force index: 强制使用
explain select * from tb_user force index(idx_user) where profession = '计科';
```



#### 5. 覆盖索引

尽量使用覆盖索引，减少select *

覆盖索引是指 查询使用了索引，并 且需要返回的列，在该索引中已经全部能够找到 

> 当有不在索引中的字段时，需要**回表查询**得到这个字段值，查询效率低
>
> 当查询的字段值在索引里时，索引创建的b+树的叶子节点有字段值和id，刚好能够返回，不用回表查询



#### 6. 前缀索引

当字段类型为字符串（varchar，text，longtext等）时，有时候需要索引很长的字符串，这会让 索引变得很大，查询时，浪费大量的磁盘IO， 影响查询效率。此时可以只将字符串的一部分前缀，建立索引，这样可以大大节约索引空间，从而提高索引效率。

```sql
-- 语法：n表示前缀长度
create index idx_xxxx on table_name(column(n));
```

索引长度n的确定：

可以根据索引的选择性来决定，而选择性是指不重复的索引值（基数）和数据表的记录总数的比值， 索引选择性越高则查询效率越高， 唯一索引的选择性是1，这是最好的索引选择性，性能也是最好的。

```sql
select count(distinct email) / count(*) from tb_user;
select count(distinct substring(email,1,5)) / count(*) from tb_user;
```



![前缀索引](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/前缀索引.67xt84uulk.webp)





#### 7. 单列索引与联合索引

- 单列索引：即一个索引只包含单个列
- 联合索引：即一个索引包含了多个列

> 使用单列索引返回多个字段的话会出现回表查询的情况，如果返回多个字段建议创建联合索引
>
>
> 联合索引是二级索引，b+树的叶子节点是挂着id的

![联合索引b+树](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/联合索引b+树.8adlw6ly5m.webp)





### 6. 索引设计原则

- 针对于数据量较大，且查询比较频繁的表建立索引
- 针对平常作为查询条件（where）、排序（order by）、分组（group by）操作的字段建立索引  
- 尽量选择区分度高的列作为索引，尽量建立唯一索引，区分度越高，使用索引的效率越高 
- 如果是字符串类型的字段，字段的长度较长，可以针对字段的特点，建立前缀索引
- 尽量使用联合索引，减少单列索引，查询时，联合索引很多时候可以覆盖索引，节省存储空间，避免回表，提高查询效率
- 要控制索引的数量，索引并不是多多益善，索引越多，维护索引结构的代价也就越大，会影响增删改的效率
- 如果索引列不能存储 NULL 值，请在创建表时使用 NOT NULL 约束它。优化器知道每列是否包含 NULL 值时，它可以更好地确定哪个索引最有效地用于查询





## SQL 优化

### 1. 插入数据优化

如果一次性要插入多条数据，可以采用以下措施优化：

1. 批量插入
2. 手动控制事务
3. 主键顺序插入



大批量（上万）插入：

```sql
-- 客户端连接服务端时，加上参数 -–local-infile
mysql –-local-infile -u root -p

-- 设置全局参数local_infile为1，开启从本地加载文件导入数据的开关
set global local_infile = 1;

-- 执行load指令将准备好的数据，加载到表结构中
load data local infile '/root/sql1.log' into table tb_user fields
terminated by ',' lines terminated by '\n' ;
```



### 2. 主键优化

数据的逻辑存储结构如图：

![InnoDB逻辑结构](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/InnoDB逻辑结构.3uv6p4jsos.webp)

在InnoDB引擎中，数据行Row是记录在逻辑结构 page 页中的，而每一个页的大小是固定的，默认16K。 那也就意味着， 一个页中所存储的行也是有限的，如果插入的数据行row在该页存储不小，将会存储 到下一个页中，页与页之间会通过指针连接。



#### 页分裂

页可以为空，也可以填充一半，也可以填充100%。每个页包含了2-N行数据(如果一行数据过大，会行 溢出)，根据主键排列。

##### 主键顺序插入：

1. 从磁盘中申请页， 主键顺序插入

![主键顺序插入1](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键顺序插入1.8s3nktvcin.webp)

2. 第一个页没有满，继续往第一页插入

![主键顺序插入2](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键顺序插入2.b9916fsip.webp)

3.  当第一个也写满之后，再写入第二个页，页与页之间会通过指针连接

![主键顺序插入3](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键顺序插入3.6f113mj197.webp)

4.  当第二页写满了，再往第三页写入

![主键顺序插入4](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键顺序插入4.13m4iwxlci.webp)



##### 主键乱序插入：

1. 加入1, 2页都已经写满了，存放了如图所示的数据

![主键乱序插入1](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键乱序插入1.9rjqy04ik2.webp)

2. 此时再插入 id 为 50 的记录，不会新开启一个页存入 50

![主键乱序插入2](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键乱序插入2.45i0k543vr.webp)

3. 因为索引结构的叶子节点是有顺序的。按照顺序，应该存储在47之后

![主键乱序插入3](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键乱序插入3.39lj4ov9l5.webp)

4. 但是47所在的1页，已经写满了，存储不了50对应的数据了。那么此时会开辟一个新的页 3

![主键乱序插入4](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键乱序插入4.1zilyddyr3.webp)

5. 但是并不会直接将50存入3页，而是会将1页后一半的数据，移动到3页，然后在3页，插入50

![主键乱序插入5](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键乱序插入5.361x6z3d4s.webp)

6. 移动数据，并插入id为50的数据之后，那么此时，这三个页之间的数据顺序是有问题的。 1的下一个页，应该是3， 3的下一个页是2。 所以，此时，需要重新设置链表指针

![主键乱序插入6](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/主键乱序插入6.8l0fpej0cl.webp)

<font color="red">上述的这种现象，称之为 "页分裂"，是比较耗费性能的操作</font>



#### 页合并

1. 目前表中已有数据的索引结构(叶子节点)如下：

![页合并1](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/页合并1.3nryvkc4e3.webp)

2. 当我们删除一行记录时，实际上记录并没有被物理删除，只是记录被标记（flaged）为删除并且它的空间变得允许被其他记录声明使用

![页合并2](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/页合并2.4xuw1vuxeu.webp)

3. 当我们继续删除2的数据记录

![页合并3](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/页合并3.6m48z2lpnv.webp)

4. 当页中删除的记录达到 MERGE_THRESHOLD（默认为页的50%），InnoDB会开始寻找最靠近的页（前或后）看看是否可以将两个页合并以优化空间使用

![页合并4](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/页合并4.67xt87dukc.webp)

5. 删除数据，并将页合并之后，再次插入新的数据21，则直接插入3页

![页合并5](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/页合并5.wiwnhs6xo.webp)

> 页合并的参数MERGE_THRESHOLD可以自己设置，在创建表或者创建索引时指定



### 3. order by 优化

排序分为两类：

1. `Using filesort`: 通过表的索引或全表扫描，读取满足条件的数据行，然后在排序缓冲区sort buffer中完成排序操作，所有不是通过索引直接返回排序结果的排序都叫 `FileSort`排序
2. `Using index`: 通过有序索引顺序扫描直接返回有序数据，这种情况即为 using index，不需要 额外排序，操作效率高

在优化order by 语句时，尽量把`Using filesort`优化为`Using index`



> 优化原则：
>
> 1. 根据排序字段建立合适的索引，多字段排序时，也遵循最左前缀法则
>
> 2. 尽量使用覆盖索引，否则需要回表查询在缓冲区中排列，还是`Using filesort`排序
>
> 3. 多字段排序, 一个升序一个降序，此时需要注意联合索引在创建时的规则（ASC/DESC）
>
>    ```sql
>    -- 按照age升序排列，phone降序排列
>    create index idx_user_age_phone_ad on tb_user (age asc, phone desc)
>    ```
>
> 4. 如果不可避免的出现filesort，大数据量排序时，可以适当增大排序缓冲区大小 `sort_buffer_size`(默认256k)



### 4. group by 优化

1. 在分组操作时，可以通过索引来提高效率
2. 分组操作时，索引的使用也是满足最左前缀法则的



### 5. limit 优化

在数据量比较大时，如果进行limit分页查询，在查询时，越往后，分页查询效率越低，因为，当在进行分页查询时，如果执行 `limit 2000000,10`，此时需要MySQL排序前 2000010 记录，仅仅返回 2000000 - 2000010 的记录，其他记录丢弃，查询排序的代价非常大

> 优化思路是<font color="red">覆盖索引+子查询</font>的方式优化



### 6. update 优化

> InnoDB的行锁是针对索引加的锁，不是针对记录加的锁 ,并且该索引不能失效，否则会从行锁升级为表锁





## 视图（P97 - P101）



## 存储过程（P102 - P115）



## 触发器（P116 - P120）





## 锁

### 1. 概述

锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算资源（CPU、 RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有 效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个 角度来说，锁对数据库而言显得尤其重要，也更加复杂。

 MySQL中的锁，按照锁的粒度分，分为以下三类： 

- 全局锁：锁定数据库中的所有表
- 表级锁：每次操作锁住整张表
- 行级锁：每次操作锁住对应的行数据



### 2. 全局锁

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML，DDL语句，已经更新操作的数据提交语句都将被阻塞

典型的使用场景是<font color="red">做全库的逻辑备份</font>，对所有的表进行锁定，从而获取一致性视图，保证数据完整性

```sql
-- 加全局锁
flush tables with read lock;

-- 数据备份（命令行指令）
mysqldump -uroot –p1234 itcast > itcast.sql

-- 释放锁
unlock tables;
```

数据库中加全局锁，是一个比较重的操作，存在以下问题：

- 如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆
- 如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导 致主从延迟



在InnoDB引擎中，我们可以在备份时加上参数 `--single-transaction` 参数来完成不加锁的一致性数据备份

> `mysqldump --single-transaction -uroot –p123456 itcast > itcast.sql`



### 3. 表级锁



### 4. 行级锁







## InnoDB 引擎





## MySQL 管理工具

### 系统数据库

| 数据库               | 含义                                                         |
| -------------------- | ------------------------------------------------------------ |
| `mysql`              | 存储 MySQL 服务器正常运行所需要的各种信息（时区、主从、用户、权限等） |
| `information_schema` | 提供了访问数据库元数据的各种表和视图，包含数据库、表、字段类型及访问权限等 |
| `performance_schema` | 为 MySQL 服务器运行时状态提供了一个底层监控功能，主要用于收集数据库服务器性能参数 |
| `sys`                | 包含了一系列方便 DBA 和开发人员利用 performance_schema 性能数据库进行性能调优和诊断的视图 |

​	

### 常用工具

1. `mysqladmin`
2. `mysqlbinlog`
3. `mysqlshow`
4. `mysqldump`
5. `mysqlimport/source`
