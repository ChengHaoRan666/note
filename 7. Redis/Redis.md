## 1. Redis十大数据类型

![redis类型](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/redis类型.8adawc2ojw.webp)



> 这里说的十大数据类型都是`val`的属性，key都是字符串



### 十大数据类型：

#### 1.1.1：字符串 String

1. string是redis最基本的类型，一个key对应一个value
2. string类型是二进制安全的，意思是redis的string可以包含任何数据，比如jpg图片或者序列化的对象 
3. string类型是Redis最基本的数据类型，一个redis中字符串 **value** 最多可以是**512M**



##### 1.1.2：字符串相关指令

`set key value [NX|XX][GET][EX seconds|PX milliseconds|EXAT unix-time-seconds|PXAT unix=time-milliseconds|KEEPTTL]` 

- `NX`-- 如果key不存在则仅设置该key
- `XX`-- 仅当key已存在时才设置它
- `GET`-- 先返回存储在 key 处的值，在修改key对应的值
- `EX` *秒数* —— 设置指定的过期时间，以秒为单位（正整数）
- `PX` *毫秒数*——设置指定的过期时间，以毫秒为单位（正整数）
- `EXAT` *秒时间戳* —— 设置以秒为单位的UNIX时间戳所对应的时间为过期时间
- `PXAT` *毫秒时间戳* —— 设置以毫秒为单位的UNIX时间戳所对应的时间为过期时间
- `KEEPTTL`——保留当前key的生存时间，即使修改值也继承之前的生存时间



```sql
set key1 val2 nx ex 10
```



> System.currentTimeMillis()可以获取当前时间戳，单位毫秒

















#### 1.2：列表 List

1. Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）
2. 它的底层实际是个双端链表，最多可以包含 2^32 - 1 个元素 (4294967295, 每个列表超过40亿个元素)





#### 1.3：哈希表 Hash

1. Redis hash 是一个 string 类型的 field（字段） 和 value（值） 的映射表，hash 特别适合用于存储对象。
2. Redis 中每个 hash 可以存储 2^32 - 1 键值对（40多亿）



#### 1.4：集合 Set

1. Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，集合对象的编码可以是 intset 或者 hashtable。
2. Redis 中Set集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。
3. 集合中最大的成员数为 2^32 - 1 (4294967295, 每个集合可存储40多亿个成员)



#### 1.5：有序结合 ZSet

1. Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
2. 不同的是每个元素都会关联一个double类型的分数，redis正是通过分数来为集合中的成员进行从小到大的排序。
3. zset的成员是唯一的,但分数(score)却可以重复。
4. zset集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。 集合中最大的成员数为 2^32 - 1





#### 1.6：地理空间 GEO

Redis GEO 主要用于存储地理位置信息，并对存储的信息进行操作，包括：

添加地理位置的坐标

获取地理位置的坐标

计算两个位置之间的距离

根据用户给定的经纬度坐标来获取指定范围内的地理位置集合



#### 1.7：基数统计 HyperLogLog

1. HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定且是很小的。
2. 在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。
3. 但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。



#### 1.8：位图 bitmap

![下载](https://github.com/ChengHaoRan666/picx-images-hosting/raw/master/下载.2vesdzla6n.webp)

由0和1状态表现的二进制位的bit数组





#### 1.9：位域 bitfield

1. 通过bitfield命令可以一次性操作多个比特位域(指的是连续的多个比特位)，它会执行一系列操作并返回一个响应数组，这个数组中的元素对应参数列表中的相应操作的执行结果。
2. 说白了就是通过bitfield命令我们可以一次性对多个比特位域进行操作。



#### 1.10：流 Stream

1. Redis Stream 是 Redis 5.0 版本新增加的数据结构
2. Redis Stream 主要用于消息队列（MQ，Message Queue），Redis 本身是有一个 Redis 发布订阅 (pub/sub) 来实现消息队列的功能，但它有个缺点就是消息无法持久化，如果出现网络断开、Redis 宕机等，消息就会被丢弃
3. 简单来说发布订阅 (pub/sub) 可以分发消息，但无法记录历史消息
4. 而 Redis Stream 提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每一个客户端的访问位置，还能保证消息不丢失







### 对 `key`  的操作：

> keys * 查看当前库所有的 key

> exists key 判断某个 key 是否存在

> type key 查看你的 key 是什么类型

> del key 删除指定的 key 数据

> unlink key 非阻塞删除，仅仅将keys从keyspace元数据中删除，真正的删除会在后续异步操作。

> ttl key 查看还有多少秒过期，-1表示永不过期，2表示已过期

> expire key 秒钟  为给定的key设置过期时间

> move key dbindex【0-15】 将当前数据库的key移动到给定的数据库db当中

> select dbindex 切换数据库【0-15】，默认为0

> dbsize 查看当前数据库key的数量

> flushdb 清空当前库

> flushall 通杀全部库







## 2. redis 持久化



## 3. redis 事务



## 4. redis管道



## 5. redis发布订阅



## 6. 主从复制



## 7. 哨兵监控



## 8. 集群分片



## 9. SpringBoot整合redis