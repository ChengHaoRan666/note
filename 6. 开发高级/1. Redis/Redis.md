## 0. redis安装

1. 到[redis官网](https://redis.io/downloads/)下载文件：redis-7.0.1.tar.gz
2. 将文件移动到Linux里的一个文件下
3. 使用`tar -zvxf redis-7.0.1.tar.gz`命令解压文件到当前文件夹
4. 到解压的文件夹内，使用`make`指令进行编译命令
5. 用`./redis-server& ./redis.conf`后台启动





## 	1. redis十大数据类型

![redis类型](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/redis类型.8adawc2ojw.webp)

> 这里说的十大数据类型都是`val`的属性，key都是字符串





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







### 十大数据类型：

#### 1.1：字符串 String

1. string是redis最基本的类型，一个key对应一个value
2. string类型是二进制安全的，意思是redis的string可以包含任何数据，比如jpg图片或者序列化的对象 
3. string类型是Redis最基本的数据类型，一个redis中字符串 **value** 最多可以是**512M**



##### 字符串相关命令

###### 1. set

`set key value [NX|XX][GET][EX seconds|PX milliseconds|EXAT unix-time-seconds|PXAT unix=time-milliseconds|KEEPTTL]` 

- `NX`-- 如果key不存在则仅设置该key
- `XX`-- 仅当key已存在时才设置它
- `GET`-- 先返回存储在 key 处的值，在修改key对应的值
- `EX` *秒数* —— 设置指定的过期时间，以秒为单位（正整数）
- `PX` *毫秒数*——设置指定的过期时间，以毫秒为单位（正整数）
- `EXAT` *秒时间戳* —— 设置以秒为单位的UNIX时间戳所对应的时间为过期时间
- `PXAT` *毫秒时间戳* —— 设置以毫秒为单位的UNIX时间戳所对应的时间为过期时间
- `KEEPTTL`——保留当前key的生存时间，即使修改值也继承之前的生存时间

> System.currentTimeMillis()可以获取当前时间戳，单位毫秒



###### 2. get

`get key`

获取key对应的 val



###### 3. mset  msetnx

`mset key value [key value]`

同时设置多个key，value

msetnx 要求所以key都不存在



###### 4. mget

`mget key1 [key2]`

同时获取多个key对应的value



###### 5. getrange    setrange

`getrange key1 0 2` 

获取key1 的值后进行截取得到最终结果，包含前面和后面

`setrange key1 0 abc`

从第0位开始（包括第0位），将他和他后面的相应的字符串替换为字符串（替换的字符串长度相同）



###### 6. incr 

> 只能数字才可以进行加减

`incr key`

将key的值加1





###### 7. incrby

`incrby key 7`

将key的值加7



###### 8. decr

`decr number1`

将number1的值减1



###### 9. decrby

`decrby number1 9`

将number1的值减9



###### 10. strlen

`strlen key`

获取key对应val的长度



###### 11. append

`append key value`

将value追加到key后面



###### 12. getset 

`getset key val`

先获取key的值，然后在修改







#### 1.2：列表 List

![image](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/image.1hs9bfbas7.webp)

1. Redis列表是简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）
2. 它的底层实际是个双端链表，最多可以包含 2^32 - 1 个元素 (4294967295, 每个列表超过40亿个元素)



##### 列表相关命令

###### 1. lpush   rpush  lrange

`lpush key list1 list2`

从左边向key中添加元素

`rpush key list11 list22`

从右边向key中添加元素

`lrange key 0 -1`

从左边遍历key ，下标从0到-1（就是全部遍历）



###### 2. lpop  rpop

`lpop key`

从左边弹出一个元素

`rpop key`

从右边弹出一个元素



###### 3. lindex

`lindex key 0`

获取key的第0个元素



###### 4. llen

`llen key`

获取key的元素个数



###### 5. lrem

`lrem key 数字n 数字v `

删除n个值为v的元素



###### 6. ltrim

`ltrim key 开始index 结束index`

截取指定范围的值在赋给key



###### 7. rpoplpush

`rpoplpush key key2`

将key的列表从右边弹出一个，从左边加入key2里



###### 8. lset

`lset key 1 2`

将下标为1的值改为2



###### 9. linert

`linsert key before 已有值 新的值`

在已有值的左边插入新的值

`linsert key after 已有值 新的值`

在已有值的右边插入新的值







#### 1.3：哈希表 Hash

1. Redis hash 是一个 string 类型的 field（字段） 和 value（值） 的映射表，hash 特别适合用于存储对象。
2. Redis 中每个 hash 可以存储 2^32 - 1 键值对（40多亿）
2. KV模式不变，但是V又是一个键值对



##### 哈希表相关命令

###### 1. hset  hget hmset hmget hgetall hdel

`hset user:001 id 11 name z3 age 25`

`hget user:001 id`

`hmset user:001 id 12 name z1 age 25  id 13 name l1 age 28`

`hmget user:001 id name age`

`hgetall user:001`

`hdel user:001 age`



###### 2. hlen

`hlen user:001`

获取key的全部数量



###### 3. hexists 

`hexists user:001 id`

判断在key中val在不在



###### 4. hkeys hvals

`hkeys user:001`

hkeys 命令用于获取哈希表中的所有字段（key）的列表

`hvals user:001`

hvals 命令用于获取哈希表中的所有值（value）的列表



###### 5. hincrby hincrbyfloat

`hincrbyfloat user:001 id 2`

hincrby 用于对整数增加数

`hincrbyfloat user:001 id 2.1`

hincrbyfloat 用于对浮点数增加数



###### 6. hetnx

`hsetnx user:001 id 20`

先判断id在不在，不在进行插入，在不做处理





#### 1.4：集合 Set

1. Redis 的 Set 是 String 类型的无序集合。集合成员是唯一的，这就意味着集合中不能出现重复的数据，集合对象的编码可以是 intset 或者 hashtable。
2. Redis 中Set集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。
3. 集合中最大的成员数为 2^32 - 1 (4294967295, 每个集合可存储40多亿个成员)



##### 集合相关命令

###### 1. smembers 

`smembers key`

遍历 key



###### 2. sadd

`sadd key 1 2 3 4`

向集合中添加元素（自动去重）



###### 3. sismember

`sismember key 5`

判断在key中元素5是否存在



###### 4. srem

`srem key 1`

在key中删除元素1



###### 5. scard

`scard key`

获取key集合中的元素个数



###### 6. srandmember 

`srandmember key1 4`

随机展现key1中的4个数，不删除



###### 7. spop

`spop key 3`

随机从集合中弹出3个数，删除



###### 8.smove 

`smove key1 key2 1`

将key1中的1删除，然后将其加入到key2中



###### 9. sdiff 

`sdiff key1 key2`

求两个集合的差值 Key1 - key2



###### 10. sunion 

`sunion key1 key2`

求两个集合的并集运算



###### 11. sinter 

`sinter key1 key2`

求两个集合的交集运算



###### 12. sintercard

`sintercard 2 key1 key2 limit 4`

求两个集合的交集运算结果的基数（个数），limit参数可选，可以限制计算的集合数量。

这里只有两个集合计算，limit参数为4，不起作用







#### 1.5：有序集合 ZSet

1. Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
2. 不同的是每个元素都会关联一个double类型的分数score，redis正是通过分数来为集合中的成员进行从小到大的排序。
3. zset的成员是唯一的,但分数(score)却可以重复。
4. zset集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)。 集合中最大的成员数为 2^32 - 1



##### 有序集合相关命令

###### 1. zadd

`zadd key1 1 k1 2 k2 3 k3`

向key1中添加值，每个值都有一个分数，会按照分数排序



###### 2. zrange 

`zrange key1 0 100`

按照分数从小到大的顺序，返回索引从0 到100 的所以元素

可以在后面加上参数==withscores==，将分数也展示出来



###### 3. zrevrange 

`zrevrange key1 0 -1`

按照分数从大到小的顺序，返回全部元素的值

可以在后面加上参数==withscores==，将分数也展示出来



###### 4. zrangebyscore

`zrangebyscore key min max [withscores] [limit offset count]`

按照分数从小到大的顺序，选择在min和max之间的元素展示

withscores 可选参数，分数从大到小排序

limit offset count  可选参数，用于限制返回结果的数量，由  offset 和 count 两个参数控制。

- offset：返回结果偏移量，即跳过多少成员。
- count：返回结果的数量。



###### 5. zscore 

`zscore key1 k1`

获取key1中k1的分数



###### 6. zcard 

`zcard key1`

获取集合中元素数量



###### 7. zrem 

`zrem key1 k1`

删除k1



###### 8. zincrby 

`zincrby key1 1 k3`

对一个val增加分数，如果没有这个val，会插入这个val和他的分数



###### 9. zcount 

`zcount key1 1 5`

获取key1中分数在1到5的元素



###### 10. zmpop 

`zmpop numkeys key [key ...] min|max [count count]`

从zset中弹出元素的指令

- numkeys：要操作的有序集合的个数
- key[key ...] 有序集合的key
- min|max  指定要弹出的最小 | 最大的元素
- count 指定要弹出的元素个数，默认为1





###### 11. zrank 

`zrank key1 k3`

获取key1中value为k3的下标（从0开始）



###### 12. zrevrank 

`zrevrank key1 k2`

逆序获取其下标值（从0开始）







#### 1.6：地理空间 GEO

Redis GEO 主要用于存储地理位置信息，并对存储的信息进行操作，包括：

添加地理位置的坐标

获取地理位置的坐标

计算两个位置之间的距离

根据用户给定的经纬度坐标来获取指定范围内的地理位置集合

GEO是Zset的子类



##### 地里空间相关命令

###### 1. geoadd 

`geoadd city 139.6917 35.6895 东京 -74.0060 40.7128 纽约`

添加



###### 2. geopos 

`geopos city 东京`

获取这个地方的经纬度坐标



###### 3. geohash

`geohash city 东京`

获取这个地方的经纬度坐标的哈希值



###### 4. geodist 

`geodist city 东京 纽约 km`

查看这两个地方的距离，单位km



###### 5. georadius

`georadius key longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] [ASC|DESC] [STORE key] [STOREDIST key]`

- `key`：地理空间索引的键名。
- `longitude`：查询点的经度。
- `latitude`：查询点的纬度。
- `radius`：查询半径。
- `m|km|ft|mi`：指定查询半径的单位，可以是米  千米  英尺（ft）或英里（mi）。



- `WITHCOORD`：返回成员的位置坐标。
- `WITHDIST`：返回成员与中心点之间的距离。
- `WITHHASH`：返回成员的52位Geohash值。
- `COUNT count`：指定返回结果的数量。
- `ASC|DESC`：根据距离排序结果，`ASC` 表示从近到远，`DESC` 表示从远到近。
- `STORE key`：将结果存储在指定的键中，而不是直接返回。
- `STOREDIST key`：将结果以及与中心点的距离存储在指定的键中。



###### 6. georadiusbymember

`georadiusbymemberkey longitude latitude radius m|km|ft|mi [WITHCOORD] [WITHDIST] [WITHHASH] [COUNT count] [ASC|DESC] [STORE key] [STOREDIST key]`

和`georadiusbymember`功能相似，只不过不必输入经纬度两个值，而是输入地址即可







#### 1.7：基数统计 HyperLogLog

1. HyperLogLog 是用来做基数统计的算法，HyperLogLog 的优点是，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定且是很小的。
2. 在 Redis 里面，每个 HyperLogLog 键只需要花费 12 KB 内存，就可以计算接近 2^64 个不同元素的基 数。这和计算基数时，元素越多耗费内存就越多的集合形成鲜明对比。
3. 但是，因为 HyperLogLog 只会根据输入元素来计算基数，而不会储存输入元素本身，所以 HyperLogLog 不能像集合那样，返回输入的各个元素。

> 就是统计去重后的个数



##### 基数统计相关命令

###### 1. pfadd 

`pfadd key 1 2 3 4 5`

添加指定元素到HyperLogLog中



###### 2. pfcount 

`pfcount key3`

返回给定 HyperLogLog 的基数



###### 3. pfmerge 

`pfmerge key3 key1 key2...`

将多个 HyperLogLog 合并为一个 HyperLogLog









#### 1.8：位图 bitmap

![下载](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/下载.2vesdzla6n.webp)

由0和1状态表现的二进制位的bit数组



##### 位图相关命令

###### 1. setbit

`set key 0 1`

将位图的第0位设置为1



###### 2. getbit

`getbit key 0`

获取位图key的第0位



###### 3. get

`get key`

获取这个位图的二进制序列，作为ASCII码转为字符格式



###### 4. strlen

`strlen key`

获取key所占大小，字节为单位。就是存储了多少01再除以8得到字节



###### 5.bitcount

`bitcount key`

获取全部键中 1 占的个数



###### 6. bitop

后面可以跟四种位操作：AND(按位与)、OR(按位或)、XOR(按位异或) 和 NOT(按位非)。

`BITOP operation destkey key [key ...]`

- operation：要执行的操作，可以是 AND、OR、XOR 或 NOT。
- destkey：存储结果的新键的名称。
- key [key ...]：一个或多个源键的名称，它们将被用于位操作。







#### 1.9：位域 bitfield

1. 通过bitfield命令可以一次性操作多个比特位域(指的是连续的多个比特位)，它会执行一系列操作并返回一个响应数组，这个数组中的元素对应参数列表中的相应操作的执行结果。
2. 说白了就是通过bitfield命令我们可以一次性对多个比特位域进行操作。





#### 1.10：流 Stream

1. Redis Stream 是 Redis 5.0 版本新增加的数据结构
2. Redis Stream 主要用于消息队列（MQ，Message Queue），Redis 本身是有一个 Redis 发布订阅 (pub/sub) 来实现消息队列的功能，但它有个缺点就是消息无法持久化，如果出现网络断开、Redis 宕机等，消息就会被丢弃
3. 简单来说发布订阅 (pub/sub) 可以分发消息，但无法记录历史消息
4. 而 Redis Stream 提供了消息的持久化和主备复制功能，可以让任何客户端访问任何时刻的数据，并且能记住每一个客户端的访问位置，还能保证消息不丢失



##### stream基本操作

###### 1.XADD 

`XADD key nomkstream maxlen approximate_length] * | ID field value [field value ...]`

- key：Stream 的名称。
- [NOMKSTREAM]：可选参数，如果指定的 Stream 不存在，且使用了 NOMKSTREAM，则命令不会创建新的 Stream。
- [MAXLEN approximate_length]：可选参数，用于限制 Stream 的最大长度。可以设置为固定长度或者使用  ~  前缀来指定一个近似长度。
- *或 ID：用于指定新条目的 ID。使用 * 时，Redis 会自动生成一个唯一的 ID。如果你想要指定自己的 ID，可以替换 *，但通常不建议这样做，因为它可能会导致数据不一致。
- field value：字段和值对，你可以添加多个字段-值对。

> id 的值比较特殊：
>
> 1733560030213-0  前面的1733560030213表示时间戳，后面的0表示在这个时间戳内的第0条



###### 2. xrange 

`xrange key - + [count 1]`

- key：Stream 的名称
- -：最小值id，可以换成具体id
- +：最大值id，可以换成具体id
- count 1：可选，限制输出条数



###### 3. xrevrange 

`xrevrange key + - [count 2]`

和xrange一样，但是顺序相反，从大到小



###### 4. xdel 

`xdel key 1733561100257-0`

删除key这个Stream中id为1733561100257-0的消息



###### 5. xlen 

`xlen key`

获取key这个Stream中消息个数



###### 6. XTRIM 

`XTRIM key MAXLEN [~] 100`

根据策略修建Stream

- key：Stream的名字
- maxlen：修建策略，maxlen表示按照数量修建
- ~ 可选：表示大概修建，可能会有误差
- 100：表示最大数量

```shell
// 保留 Stream 中最新的消息，直到总字节大小达到 1MB：
XTRIM key MAXLEN BYTES 1048576
```



###### 7. xread

`XREAD [COUNT count] [BLOCK milliseconds] STREAMS key [key ...] id [id ...]`

- COUNT count：可选参数，指定返回的最大消息数量。
- BLOCK milliseconds：可选参数，指定阻塞等待的时间（以毫秒为单位）。如果不指定，则 XREAD 为非阻塞模式。
- STREAMS key [key ...]：指定要读取的 Stream 键名，可以同时指定多个 Stream。
- id [id ...]：指定每个 Stream 的起始消息 ID。使用 `$` 表示从最后一个已知消息之后开始读取，或者指定具体的消息 ID。







## 2. redis 持久化

两种方法实现持久化：RDB，AOF



### RDB：

#### 基础信息：

- **工作原理**：快照。一种基于时间的数据快照，在指定的时间间隔内，redis会生成一个.rdb的快照文件，里面包含了数据库在那一时刻的所有文件。
- **存储地点**：存储在`dump.rdb`文件里，文件位置和文件名字可以设置。
- **触发方式**：可以通过配置定时自动执行，也可以通过SAVE或BGSAVE命令手动触发。
  - SAVE命令会阻塞Redis服务器进程，直到RDB文件被创建完毕。
  - BGSAVE命令会派生出一个子进程来创建RDB文件，而父进程继续处理命令，这减少了服务中断的时间。
- **优点**：
  - 数据恢复速度快，因为只需载入一个单独的文件。
  - 文件紧凑，占用空间小，便于传输。
- **缺点**：
  - 在两次快照之间如果发生故障，可能会导致数据丢失。
  - 创建快照可能会对性能产生影响，尤其是大数据集。



```conf
 在redis.conf配置文件中的默认配置（7.0版本）
# Unless specified otherwise, by default Redis will save the DB:
#   * After 3600 seconds (an hour) if at least 1 change was performed
#   * After 300 seconds (5 minutes) if at least 100 changes were performed
#   * After 60 seconds if at least 10000 changes were performed

#除非另有说明，否则默认情况下Redis将保存数据库：
#*3600秒（一小时）后，如果至少进行了一次更改
#*300秒（5分钟）后，如果执行了至少100次更改
#*60秒后，如果执行了至少10000次更改
```



#### 自己配置：

配置保存条件：

```conf
442  # save 3600 1 300 100 60 10000
443  # 下面使用自己的配置信息覆盖默认的配置,如果5秒钟2次修改就生成一个rdb文件
444  save 5 2
```

配置rdb文件保存路径：

```conf
505 # Note that you must specify a directory here, not a file name.
506 # dir ./
507 # 设置保存的路径在当前文件夹下的dumpfiles文件夹下
508 dir /opt/soft/redis-7.0.1/dumpfiles
```

配置rdb文件保存的名字：

```conf
482 # The filename where to dump the DB
482 # 配置保存的rdb文件名字
482 dbfilename MyDump.rdb
```





#### 触发条件：

##### 自动触发：

当满足配置文件中设置的保存条件时，redis会自动保存当前快照到指定位置

> 执行 flushall 或 flushdb 命令也会生成一个rdb快照，但是无意义，里面是空的

> 主从复制时，主节点也会自动触发

> 执行shutdown命令且没有设置开启AOF持久化也会自动触发

##### 手动触发：

`save` `bgsave` 两个命令手动触发备份文件

> save 会阻塞主进程，当使用 save 命令后，主进程的redis服务将暂停，不能处理其他命令
>
> bgsave 不会阻塞主进程，会在后台异步进行快照操作

<font color="red">实际生产中只能使用`bgsave`</font>

通过`lastsave`命令可以获取最后执行`bgsave`命令的时间戳





#### 恢复流程：

会自动识别dir配置的文件夹下的dump.rdb文件进行备份

<font color="red">但是在关机时redis也会生产一个rdb文件，会覆盖掉有数据的那个备份，所有备份与生产要物理隔离</font>





#### 检查修复：

`./redis-check-rdb /opt/soft/redis-7.0.1/dumpfiles/MyDump.rdb `

通过`redis-check-rdb`命令可以检查并修复rdb文件





#### 禁用情况：

在配置文件中写入 `save ""`即可禁用RDB 







### AOF：

#### 基础信息：

- **工作原理**：每当执行一个改变数据集的命令时，这个命令就会被追加到AOF文件的末尾。重启时，Redis可以通过重新执行AOF文件中的所有写命令来恢复数据。
- **存储地点**：存储在`appendonly.aof`文件内。
- **触发方式**：AOF是持续的。不过，可以通过配置设置同步频率。
- **优点**：
  - 数据安全性更高，因为记录的是每条命令，即使发生故障，丢失的数据也相对较少。
  - 如果日志文件变得过大，Redis可以在后台重写日志，以减少文件大小。
- **缺点**：
  - 相比RDB，AOF文件通常更大，数据恢复可能更慢。
  - 在高负载下，AOF可能会比RDB慢。



#### 自己配置：

1. 开启
   AOF在redis7.0是默认关闭的`appendonly no` ，需要开启`appendonly yes`

2. AOF文件名：

   ```conf
   1412 appendfilename "appendonly.aof"
   ```

3. 写回策略：

   ```conf
   1442 # 写回策略，默认everysec
   1443 # appendfsync always
   1444 appendfsync everysec
   1445 # appendfsync no
   ```

4. AOF保存路径：
   AOF的保存路径是由dir配置和appenddirname配置组成的，appenddirname文件夹放在dir里，aof文件放在appenddirname里。dir配置是在rdb时配置的。

   ```conf
   1418 appenddirname "appendonlydir" 
   ```

   

> AOF文件在7.0之后有了变更，由 1 个文件变为三个文件
>
> 由三部分构成：
>
> - appendonly.aof.1.base.rdb   base基本文件，最多1个
>
> - appendonly.aof.1.incr.aof      incr增量文件，可以多个
>
> - appendonly.aof.manifest       manifest清单文件，会自动清除



#### 工作流程：

1. 进行增删改查命令
2. 对于其中的增删改命令，将这些命令放入AOF缓冲区中，在缓冲区中累计一定量的命令在存入aof文件中，目的是避免频繁的与磁盘进行IO操作
3. AOF缓冲会根据AOF缓冲区同步文件的<font color="red">三种写回策略</font>将命令写入磁盘上的AOF文件。
4. 随着写入AOF内容的增加为避免文件膨胀，会根据规则进行命令的合并(又称<font color="red">AOF重写</font>)，从而起到AOF文件压缩的目的。
5. 当Redis Server 服务器重启的时候会从AOF文件载入数据。



#### 写回策略：

三种：always  everysec   no

##### 1. always

同步回写，每写一次都回更新aof文件

##### 2. everysec(redis 默认)

每秒写回

##### 3. no

操作系统控制的写回，每个写命令执行完，把日志写入AOF缓冲区中，由操作系统决定何时将缓冲区内容写入磁盘

![AOF写回策略](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/AOF写回策略.esk64ws0w.webp)



#### 检查修复：

```conf
./redis-check-aof --fix /opt/soft/redis-7.0.1/myredis/appendonlydir/appendonly.aof.1.incr.aof 
```

通过`redis-check-aof`命令检查AOF文件是否正确，通常只用检查incr文件即可

--fix 参数表示在检查后还会尝试修复，修复可能会出问题，建议备份





#### 重写机制：

启动AOF文件的内容压缩，只保留可以恢复数据的最小指令集



##### 配置参数：

auto-aof-rewrite-percentage：指定当前AOF文件大小是上次重写后大小的一定百分比时，触发

auto-aof-rewrite-min-size：指定AOF文件的最小大小，只有当AOF文件大小超过后，触发

```conf
1486 auto-aof-rewrite-percentage 100
1487 auto-aof-rewrite-min-size 64mb
```

<font color="red">两个配置是且的关系，同时满足才触发</font>



##### 重写原理：

1. 在重写开始前，redis会创建一个“重写子进程”，这个子进程会读取现有的AOF文件，并将其包含的指令进行分析压缩并写入到一个临时文件中。
2. 与此同时，主进程会将新接收到的写指令一边累积到内存缓冲区中，一边继续写入到原有的AOF文件中，这样做是保证原有的AOF文件的可用性，避免在重写过程中出现意外。
3. 当“重写子进程”完成重写工作后，它会给父进程发一个信号，父进程收到信号后就会将内存中缓存的写指令追加到新AOF文件中
4. 当追加结束后，redis就会用新AOF文件来代替旧AOF文件，之后再有新的写指令，就都会追加到新的AOF文件中
5. 重写aof文件的操作，并没有读取旧的aof文件，而是将整个内存中的数据库内容用命令的方式重写了一个新的aof文件，这点和快照有点类似



##### 触发方式：

###### 1. 自动触发

满足配置参数时，Redis会自动重写

###### 2. 手动触发

使用`bgrewriteaof`命令手动触发

> 也就是说 AOF 文件重写并不是对原文件进行重新整理，而是直接读取服务器现有的键值对，然后用一条命令去替代之前记录这个键值对的多条命令，生成一个新的文件后替换原来的 AOF 文件。







### 混合持久化：

#### 开启混合持久化配置：

```conf
1515 # 是否开启AOF和RDB两种持久化方式混合
1516 aof-use-rdb-preamble yes
```



#### 混合模式下数据加载流程：

先判断是否存在AOF，如果存在，加载AOF，如果不存在AOF，判断是否存在RDB，存在加载，不存在失败。

![混合模式加载流程](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/下载.syzx4gz5x.webp)

<font color="red">推荐使用混合模式</font>

<font color="red">混合模式前提是开启AOF</font>

> RDB镜像做全量持久化，AOF做增量持久化
>
> 先使用RDB进行快照存储，然后使用AOF持久化记录所有的写操作，当重写策略满足或手动触发重写的时候，将最新的数据存储为新的RDB记录。这样的话，重启服务的时候会从RDB和AOF两部分恢复数据，既保证了数据完整性，又提高了恢复数据的性能。简单来说：混合持久化方式产生的文件一部分是RDB格式，一部分是AOF格式





### 纯缓存模式：

同时关闭RDB和AOF

```conf
# 关闭RDB
save ""
# 关闭AOF
appendonly no
```

>在禁用RDB持久化模式下，我们仍可以使用save，bgsave命令生成RDB文件

>  在禁用AOF持久化模式下，我们仍可以使用bgrewriteaof命令生成AOF文件

> 在纯缓存模式下，默认不读取RDB和AOF文件





## 3. redis 事务

可以一次执行多个命令，本质是一组命令的集合，一个事务中的所有命令都会序列化，按顺序的串行化执行而不会被其他命令插入，不允许加塞。



#### 命令：

`MULTI`：开启事务功能，开启后输入命令会将这些命令放入一个队列中，不立即执行。

`EXEC`：当开启事务功能后，会执行所有在MULTI命令后的命令，如果有错误不会执行任何命令。

`DISCARD`：取消事务，放弃执行事务快的所有命令。

`WATCH key [key...]`：监视一个或多个key，如果在事务执行之前这个（或这些）key被其他命令所改动，那么事务将被打断。

`UNWATCH`：取消WATCH命令对所有key的监视。



#### redis事务和数据库事务的区别：

| 区别点             |                                                              |
| ------------------ | ------------------------------------------------------------ |
| 单独的隔离操作     | Redis的事务仅仅是保证事务里的操作会被连续独占的执行，redis命令执行是单线程架构，在执行完事务内所有指令前是不可能再去同时执行其他客户端的请求的 |
| 没有隔离级别的概念 | 因为事务提交前任何指令都不会被实际执行，也就不存在”事务内的查询要看到事务里的更新，在事务外查询不能看到”这种问题了 |
| 不保证原子性       | Redis的事务不保证原子性，也就是不保证所有指令同时成功或同时失败，只有决定是否开始执行全部指令的能力，没有执行到一半进行回滚的能力 |
| 排它性             | Redis会保证一个事务内的命令依次执行，而不会被其它命令插入    |





#### 特殊情况：

##### 1. 全体连坐

当命令本身错误（如语法等），这时候提交EXEC也不能执行，全部命令都不会执行



##### 2. 冤头债主

当命令语法无错误，但是运行时有错误（如非整型自增等），这时候提交其他命令可以执行，但是错误命令不能执行。



> redis没有事务回滚功能，当发生冤头债主情况时，需要自己回滚回去



##### 3. 监控

在事务发生前监控一个或多个值，如果这些值发生改变，那么这个事务将无效，在事务里写的命令也将全部失效。监控也在继续。提交错误是会返回 nui

监控会在下面条件下失效：

1. EXEC命令成功执行，监控自行解除
2. 遇到UNWATCH命令显式解除监控
3. 客户端连接丢失时，所以监控都将丢失







## 4. redis管道

管道(pipeline)可以一次性发送多条命令给服务端，服务端依次处理完完毕后，通过一条响应一次性将结果返回，通过减少客户端与redis的通信次数来实现降低往返延时时间。pipeline实现的原理是队列，先进先出特性就保证数据的顺序性。

管道是为了优化频繁命令往返造成的等待时间，是将命令一次发送完成，对其他不造成影响

类似于redis原生的命令`mget`，`mset`

```shell
root@iZbp16jrgpn579tczps0e4Z:/home/text# cat cmd.txt | /opt/soft/redis-7.0.1/src/redis-cli -a 123456 --pipe
Warning: Using a password with '-a' or '-u' option on the command line interface may not be safe.
All data transferred. Waiting for the last reply...
Last reply received from server.
errors: 0, replies: 5
```

在linux里创建一个文件夹，里面放着redis命令，通过管道方式`--pipe`传入redis里执行。



#### pipeline与原生批量命令对比：

1. <font color="red">原生批量命令是原子性，pipeline是非原子的</font>
2. 原生批量命令一次执行一种命令，pipeline支持批量执行不同的命令
3. 原子批命令是服务端实现的，pipeline需要服务端与客户端共同完成



#### pipeline与事务命令对比：

1. 事务具有原子性，管道不具有原子性
2. 管道一次性会将多条命令发送到服务器的，事务是一条一条发送，只在收到exec命令后才会执行



#### pipeline注意事项：

1. pipeline缓冲的指令只是会依次执行，不保证原子性，如果执行中指令发生异常，将会继续执行后续的指令
2. 使用pipeline组装的命令个数不能太多，不然数据量过大客户端阻塞的时间可能过久，同时服务端此时也被回复一个队列答复，占用很多内存







## 5. 主从复制 replica

> 实现多台机器redis的同步，主redis以写为主，从redis以读为主。从redis可以有多台。



#### 能干什么：

1. 读写分离
2. 容灾恢复
3. 数据备份
4. 水平扩容支撑高并发



#### 配置方式：

<font color="red">配置从redis库不配主redis库</font>

master如果配置了requirepass参数，需要密码登录，那么slave就要配置masterauth来设置校验密码，否则master会拒绝slave的访问。



#### 操作命令：

1. info replication  可以查看复制节点的主从关系和配置信息
2. replicaof 主库IP  主库端口（一般写入redis.conf配置文件中）
3. slaveof  主库IP 主库端口（命令方式配置主库，每次与master断开后，都需要执行重新连接，除非写入redis.conf配置文件中。在运行期间修改slave节点的信息，如果该数据库已经是某个主数据库的从数据库，那么会停止和原主数据库的同步关系转而和新数据库同步。）
4. slaveof  no one  使当前数据库停止与其他数据库的同步，转为主数据库





#### 配置实例：

1. 开启daemonize yes（开启进程守护，当终端关闭时redis也在运行）
2. 注释bind 127.0.0.1（允许其他ip连接）
3. protected-mode no（关闭保护模式，允许外部链接）
4. 指定端口
5. 指定当前工作目录，dir
6. pid文件名字，pidfile
7. log文件名字，logfile（日志文件地址）
8. requirepass（设置连接Redis服务器时需要提供的密码）
9. dump.rdb名字（指定Redis的RDB持久化文件的名称）
10. aof文件，appendfilename（指定Redis的AOF（Append Only File）持久化文件的名称）
11. <font color="red">从机访问主机的通行密码masterauth，必须</font>
11. <font color="red">指定主库IP replicaof 主库IP  主库端口（一般写入redis.conf配置文件中）</font>





#### 一主二从

##### 方案一：配置文件写死

执行第四步的配置实例后，在从节点上配置文件中 replicaof 主库IP  主库端口。

先启动master节点，在启动slave节点即可启动。



##### 方案二：命令方式手动指定

执行第四步的配置实例后，在从节点上命令行配置 slaveof  主库IP 主库端口。

使用命令方式手动指定重启后主从关系就不存在了。



<font color="red">常见问题：</font>

1. <font color="red">只能在主节点进行写操作，从节点只可以读操作</font>
2. <font color="red">主机shutdown后，从机不会上位，数据保留</font>
3. <font color="red">主机shutdown后，重启后主从关系还在，还可以顺利复制</font>
4. <font color="red">从机shutdown后，主机还在继续，从机重启后还可以跟上主机</font>





#### 薪火相传

上一个slave从节点可以是下一个slave从节点的master主节点，slave同样可以接受其他slaves的链接和同步请求，那么该slave作为链条中的下一个master，可以有效减轻主master的写压力。

> 中间的转移节点同样不能进行写操作，只是更改了同步的方向，减轻主master的压力。





#### 反客为主

使用 slaveof  no one  指令让当前数据库停止与其他数据库的同步，转为主数据库。





#### 复制原理和工作流程：

1. 从机启动，同步初请：从机启动成功连接主机后会发送一个sync命令，首次全新连接主机后，一次完全同步（全量复制）将自动执行，从机自身数据将会被覆盖。
2. 首次连接，全量复制：主节点收到sync命令后会在后台保存快照（RDB持久化），同时收集所有接收到的用于修噶数据集的命令缓存起来，主节点执行RDB持久化后，主节点会将rdb快照文件和所有缓存的数据发送到从节点完成一次完全同步。
3. 心跳持续，保持通信：`repl-ping-replica-period 10`，配置文件中心跳时间10s
4. 进入平稳，增量复制：主机会将所有执行修改的命令同步到从机实现同步。
5. 从机下线，重连续传：主机会检查backlog里的offset（偏移量），将从机未同步的数据传给从机实现同步。



#### 主从复制的缺点

1. 复制延迟，信号衰减
2. master挂了处理麻烦







## 6. 哨兵监控 sentinel

#### 是什么：

在主从复制方式中，存在一个问题：如果主库出现问题shutdown后，那么从库不会自动切换，哨兵机制可以有效的解决这个问题。

哨兵监控室吹哨人巡查监控后台master主机是否故障，如果故障根据<font color="red">投票数</font>自动将某一个从库转为新主库，继续对外服务。

![redis哨兵](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/redis哨兵.26lkjenc1l.jpg)





#### 能干什么：

1. 主从监控：监控主从redis库是否运行正常。
2. 消息通知：哨兵可以将故障转移的结果发送给客户端。
3. 故障转移：如果master异常，会进行主从切换，将其中一个slave作为新master。
4. 配置中心：客户端通过连接哨兵来获得当前redis服务的主节点地址。



#### 配置方式：

> 采用三个哨兵，一主二从架构。六台机器

基本配置（redis.conf配置）：

1. 开启daemonize yes（开启进程守护，当终端关闭时redis也在运行）
2. 注释bind 127.0.0.1（允许其他ip连接）
3. protected-mode no（关闭保护模式，允许外部链接）
4. 指定端口
5. 指定当前工作目录，dir
6. pid文件名字，pidfile
7. log文件名字，logfile（日志文件地址）
8. requirepass（设置连接Redis服务器时需要提供的密码）
9. dump.rdb名字（指定Redis的RDB持久化文件的名称。）
10. aof文件，appendfilename（指定Redis的AOF（Append Only File）持久化文件的名称）

主从配置（从节点redis.conf文件配置）：

1. 从机访问主机的通行密码masterauth，必须

2. 指定主库IP replicaof 主库IP  主库端口（一般写入redis.conf配置文件中）

哨兵配置（sentinel.conf文件配置）：

1. 配置监听端口bind
1. 开启进程守护daemonize yes
1. 关闭保护模式protected-mode no
1. 设置端口prot 26379
1. 设置日志目录logfile
1. 设置pid目录pidfile
1. 绑定节点 sentinel monitor mymaster 127.0.0.1 6379 2
1. 设置连接密码  sentinel auth-pass \<master-name> \<password>



#### 启动哨兵：

> redis-sentinel sentinel.conf --sentinel

<font color="red">启动哨兵后实现了自动化启动，选举。在这个过程中配置文件会被哨兵修改</font>





#### 哨兵运行流程和选举原理：

当一个主从配置中的master失效后，哨兵可以选举出一个新的master用于自动接替原master的工作，主从配置中的其他redis服务器自动只想新的master同步数据。一般建议哨兵采用奇数台，防止某一台哨兵无法连接到master导致误切换。



##### 主观下线SDown：

单个哨兵自己主观上检测master的状态，从哨兵角度来看：发送ping心跳后没有回应就达到了主管下线SDown的条件。

在配置文件中配置了判断主管下线的时间：`sentinel down-after-milliseconds mymaster 30000`默认30s



##### 客观下线ODown：

多个哨兵通过协商达成一定意见后才能将一个master节点转为客观下线状态。需要哨兵数也在配置文件中配置：`sentinel monitor mymaster 127.0.0.1 6379 2`



##### 哨兵中选出Leader：

当master客观下线时，哨兵群会选择出一个哨兵Leader 进行<font color="blue">故障迁移</font>：对从节点进行操作，选出一个新master。

选举出leader哨兵的算法：RAFT算法



##### leader哨兵进行故障转移：

![slave节点选择](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/slave节点选择.6pnll6l5qc.webp)

1. 先比较优先级，谁的优先级高就选谁做master。一样的情况下向下判断。
   优先级由`replica-priority 100`指定，值越小，优先级越高。
2. 再比较复制偏移量，谁大选谁做master。一样的情况下向下判断。
3. 在比较RunID，谁小选谁。



得到新的节点后：

1. leader 哨兵会对选举出的新 master 执行 `slaveof no one` 操作，将其提升为 master 节点
2. leader 哨兵向其它 slave 发送命令，让剩余的 slave 成为新 master 节点的 slave



leader哨兵会更新所有哨兵的配置文件，广播新master的信息。如果配置了 `notification-script` 或 `client-reconfig-script`会通知系统管理员或客户端。







#### 哨兵使用建议：

- 哨兵节点的数量应为多个，哨兵本身应该集群，保证高可用
- 哨兵节点的数量应该是奇数
- 各个哨兵节点的配置应一致
- 如果哨兵节点部署在 Docker 等容器里面，尤其要注意端口的正确映射
- 哨兵集群 + 主从复制，并不能保证数据零丢失





## 7. redis集群 cluster

#### 是什么：

在主从复制+哨兵的操作方式中，都是一台master进行处理，如果主节点挂了，即使采用哨兵自动升级子节点，也会有一段时间的操作没有处理到。对此集群方式将所有数据不再一台机器上，而是每台机器只存有部分数据。


![redis集群](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/redis集群.esloi3z5k.jpg)

集群模式几乎完全替代了哨兵模式，当M1挂了后，可以由M2，M3提供服务，S1还可以顶替M1。

> redis集群是一个提供在多个Redis节点间共享数据的数据集，redis集群可以支持多个Master



#### 能干什么：

1. Redis集群支持多个Master，每个Master又可以挂载多个Slave，从而实现了读写分离，支持数据的高可用，支持海量数据的读写存储操作。
2. 由于集群自带哨兵的故障转移机制，内置了高可用的支持，<font color="red">无序再使用哨兵功能</font>
3. 客户端与Redis的节点连接，不再需要连接集群中所有节点，只需要任意连接集群中的一个可用节点即可。
4. <font color="red">槽位slot</font>负责分配到各个物理服务节点，由对应的集群来负责维护节点，插槽和数据之间的关系。



#### 槽位slot：

redis集群使用哈希槽来存储键值对，槽位固定16384个，但是官网建议最多1000个节点。

对于每一个键值对，通过哈希函数`CRC16(key) % 16384`映射到对应的槽位上。

集群中的每个主节点负责一部分槽位，即每台redis机器上只存储一部分数据。<font color="red">**（分片）**</font>





#### 分片+槽位的优势：

1. 存入数据和取出数据方便
2. 方便缩扩容
   假如有ABC三个redis，现在要添加第四个Dredis，我们只需要将ABC三个redis管理的槽位减少匀给D即可。同理，如果要删除redis，只需要将这个redis管理的槽位匀给其他redis即可。
   <font color="red">增加节点和删除节点都不会造成集群不可用状态，可用性增加</font>





#### 哈希槽映射方案：

##### 哈希取余分区：

记录数%机器数得到存放读取机器。



**优点**： 简单粗暴，直接有效，只需要预估好数据规划好节点，例如3台、8台、10台，就能保证一段时间的数据支撑。使用Hash算法让固定的一部分请求落到同一台服务器上，这样每台服务器固定处理一部分请求（并维护这些请求的信息），起到负载均衡+分而治之的作用。

**缺点**：  原来规划好的节点，进行扩容或者缩容就比较麻烦了额，不管扩缩，每次数据变动导致节点有变动，映射关系需要重新进行计算，在服务器个数固定不变时没有问题，如果需要弹性扩容或故障停机的情况下，原来的取模公式就会发生变化：Hash(key)/3会变成Hash(key) /?。此时地址经过取余运算的结果将发生很大变化，根据公式获取的服务器也会变得不可控。某个redis机器宕机了，由于台数数量变化，会导致hash取余全部数据重新洗牌。



##### 一致性哈希算法分区：

为了解决分布式缓存数据变动和映射问题，某个机器宕机了，分母数量改变了，自然取余数不对的问题。

 为了尽可能减少当机器个数发生改变时，客户端到服务器的映射关系



分为三部分：

1. 算法构建一致性哈希环
2. redis服务器ip节点映射
3. key落到服务器的落键规则



**优点**：

加入和删除节点只影响哈希环中顺时针方向的相邻的节点，对其他节点无影响。

 

**缺点**： 

数据的分布和节点的位置有关，因为这些节点不是均匀的分布在哈希环上的，所以数据在进行存储时达不到均匀分布的效果。



##### 哈希槽分区：

在数据和redis节点之间加了一层槽，数据和哈希槽是绑定的，通过算法`slot=CRC16(key)mod16384`计算出键在那个槽上，然后找管理这个槽的redis节点。



#### 为什么redis集群哈希槽数是16384个？

(1)如果槽位为65536，发送心跳信息的消息头达8k，发送的心跳包过于庞大。

> 在消息头中最占空间的是myslots[CLUSTER_SLOTS/8]。 当槽位为65536时，这块的大小是: 65536÷8÷1024=8kb 

> 在消息头中最占空间的是myslots[CLUSTER_SLOTS/8]。 当槽位为16384时，这块的大小是: 16384÷8÷1024=2kb 

因为每秒钟，redis节点需要发送一定数量的ping消息作为心跳包，如果槽位为65536，这个ping消息的消息头太大了，浪费带宽。

 

(2)redis的集群主节点数量基本不可能超过1000个。

集群节点越多，心跳包的消息体内携带的数据越多。如果节点过1000个，也会导致网络拥堵。因此redis作者不建议redis cluster节点数量超过1000个。 那么，对于节点数在1000以内的redis cluster集群，16384个槽位够用了。没有必要拓展到65536个。



(3)槽位越小，节点少的情况下，压缩比高，容易传输

Redis主节点的配置信息中它所负责的哈希槽是通过一张bitmap的形式来保存的，在传输过程中会对bitmap进行压缩，但是如果bitmap的填充率slots / N很高的话(N表示节点数)，bitmap的压缩率就很低。 如果节点数很少，而哈希槽数量很多的话，bitmap的压缩率就很低。 





<font color="red">redis集群不保证强一致性，这意味着在特定的条件下，redis集群可能会丢失一些被系统收到的写入命令</font>





#### 集群环境配置：

![redis集群](https://ChengHaoRan666.github.io/picx-images-hosting/Redis/redis集群.esloi3z5k.jpg)

##### 搭建三主三从架构：

1. 配置三个主节点从节点配置：
   <font color="red">三个主节点和三个从节点配置一样的，不需要从节点配置replicaof</font>

   > bind 0.0.0.0
   > daemonize yes
   > protected-mode no
   > port 6381
   > logfile "/myredis/cluster/cluster6381.log"
   > pidfile /myredis/cluster6381.pid
   > dir /myredis/cluster
   > dbfilename dump6381.rdb
   > appendonly yes
   > appendfilename "appendonly6381.aof"
   > requirepass 111111
   > masterauth 111111
   >
   > cluster-enabled yes // 开启集群模式
   > cluster-config-file nodes-6381.conf //指定集群节点配置文件（保存当前节点状态，自动生成，不要手动编辑）
   > cluster-node-timeout 5000 // 集群超时时间（以毫秒为单位）

2. 启动六台redis

3. 通过`redis-cli`命令为6台机器构建集群关系

   > redis-cli -a 111111 --cluster create --cluster-replicas 1 \
   > 192.168.111.175:6381 192.168.111.175:6382 \
   > 192.168.111.172:6383 192.168.111.172:6384 \
   > 192.168.111.174:6385 192.168.111.174:6386
   >
   > -a：密码
   > --cluster create ：表示要创建集群
   > --cluster-replicas 1：指定每个主节点有多少个从节点

4. 查看节点状态
   `info replication`：查看一个节点的主从关系。
   `cluster info`：用于查看整个 Redis 集群的状态信息。
   `cluster nodes`：用于查看集群中所有节点的详细信息，包括节点的角色、状态、槽位分配情况等。



##### 读写操作：

如果只是完成上面配置，会导致在一个主节点可以写k1，不能写k2，在另一个主节点能写k2，不能写k1。这是因为每个节点管理的槽位不同的，我们写入一个key，会计算这个key在哪个槽，如果我们写入的这个主节点不是管理这个槽位的，无法写入。

<font color="red">解决方法：我们可以在启动时加上一个参数 -c</font>

> redis-cli -a 123456 -p 6381 -c
>
> -a：密码
> -p：端口号
> -c：自动路由配置



##### 主从容错切换：

主节点down掉之后，从节点会自动上位成为主节点。

从节点上位后，原本主节点再加入集群后会作为从节点，成为三主三从。

节点从属调整：在从节点执行`CLUSTER FAILOVER`指令实现主从节点平稳调整



##### 主从扩展：

1. 新开两台redis，一台作为主节点，一台作为从节点。分别启动。
2. 将新增的主节点加入原有集群`redis-cli -a 密码 --cluster add-node 新增主节点IP：端口号  集群主节点IP：端口号`
3. 通过`redis-cli -a 密码 --cluster check IP：prot`查看哈希槽分配情况。这时加入的新节点并没有哈希槽。
4. 重新分派槽号命令:`redis-cli -a 密码 --cluster reshard IP地址:端口号`
5. 再次查看哈希槽分配情况。
6. 为新加入主节点分配从节点，`redis-cli -a 密码 --cluster add-node ip:新slave端口 ip:新master端口 --cluster-slave --cluster-master-id 新主机节点ID`

> 在第三步时，新节点加入并没有槽位，通过分配槽位命令才有了槽位。这个槽位并不是所有主节点进行重新分配，这样代价太大，而是有槽位的主节点每个分出一个区间给新节点，也就是说新节点的槽位不是连续的。



##### 主从缩容：

清除一个主节点和对应的从节点。

1. 先获取从节点的ID `redis-cli -a 密码 --cluster check IP：prot`
2. 在集群中将从节点删除  `redis-cli -a 密码 --cluster del-node 从机 ip:从机端口 从机节点ID`
3. 将主节点的槽位进行分配给其他主节点`redis-cli -a 111111 --cluster reshard 待删除主节点IP：prot`，会提示是否删除和输入接收槽位的主节点ID
4. 删除主节点  `redis-cli -a 密码 --cluster del-node 主机 ip:主机端口 主机节点ID`



##### 通识占位符：

不在同一个slot槽位下的键值无法使用mset、mget等多键操作

可以通过{}来定义同一个组的概念，使key中{}内相同内容的键值对放到一个slot槽位去，对照下图类似k1k2k3都映射为x，自然槽位一样





## 8. SpringBoot整合redis

#### 1. jedis

最老牌的



#### 2. lettuce

改善了jedis的bug



#### 3.RedisTemplate

<font color="red">SpringBoot推荐使用这个，整合了上面两个，再做了一个封装</font>

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

