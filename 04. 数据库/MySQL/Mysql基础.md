## **1. 表和数据库的创建和删除**

### 数据库操作：

```mysql
# 创建数据库，在不存在
create database [IF NOT EXISTS] 数据库名称;

# 删除数据库
drop database 数据库名称;

# 删除数据库，检查是否存在
drop database if exists 数据库名称;

# 使用数据库
use mysql_text_one;

# 列出数据库管理系统的数据库列表
show databases;

# 查询当前数据库
select database();
```

### 表操作：

```mysql
# 显示指定数据库的所有表，使用该命令前需要使用 use 命令来选择要操作的数据库。
SHOW TABLES;

# 显示数据表的属性，属性类型，主键信息 ，是否为 NULL，默认值等其他信息。
SHOW COLUMNS FROM 数据表;

# 显示数据表的属性，属性类型，主键信息 ，是否为 NULL，默认值等其他信息。
desc 数据表名;

# 显示数据表的详细索引信息，包括PRIMARY KEY（主键）。
SHOW INDEX FROM 数据表;


# 创建表
create table 表名(列名1 数据类型 列级约束类型 [comment '注释'],
                 列名2 数据类型 列级约束类型  [comment '注释'],
                 列名3 数据类型 列级约束类型  [comment '注释'],
                表级约束
                 )comment ['注释'];
                 
                 
-- 删除表，如果存在的话
DROP TABLE IF EXISTS 表名;


-- 直接删除表，不检查是否存在
DROP TABLE 表名;

# 查看建表语句
show create table one_table;

# 为表添加字段
alter table 表名 add 字段名 char(3) comment '注释';

# 修改字段数据类型
alter table 表名 modify 字段名 新数据类型;

# 修改字段名和字段数据类型
alter  table 表名 change 旧字段名 新字段名 新数据类型 comment '注释';

# 删除字段
alter table 表名 drop 字段名;

# 修改表名
alter table 旧表名 rename to 新表名;
```



### 数据结构：

#### 数值类型数据结构：

![数值类型](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/数值类型.45lufddy6.webp)



#### 时间类型数据结构：

![时间类型](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/时间类型.2veo2hzhzx.webp)

#### 字符串类型数据结构：

![字符串类型](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/字符串类型.2obg72dckl.webp)





## **2. 表的增删改查**

### 增：

```mysql
# 给指定字段添加数据
insert into 表名(字段名1, 地段名2, 字段名3...) values (值1，值2，值3...);

# 给全部字段添加数据
insert into one_table values ('张三','0101','18');

# 给全部字段批量添加属性
insert into 表名 values (值1, 值2, 值3),(值1, 值2, 值3)...;

# 给指定字段批量添加数据
insert into 表名(字段名1, 地段名2...) values (值1，值2...),(值1，值2...)...;
```

### 删：

```mysql
# 删除一行数据
delete from 表名 where 条件;
```

### 改：

```mysql
update 表名 set 字段名1 = 值1,字段名2 = 值2 where 条件;
```

### 查：

#### **1. 单表查询：**

```mysql
# 查询语句
select [distinct] 字段列表
from 表名列表
where 条件列表
group by 分组条件
having 分组后条件列表
order by 字段1 asc 或 desc，字段2 asc 或 desc...
limit 起始索引，每页多少条数据;
```

| 运算符 |   功能   |      运算符      |                  功能                  |
| :----: | :------: | :--------------: | :------------------------------------: |
|   >    |   大于   | between...AND... |  在范围内<br/>（包含最小值，最大值）   |
|   <    |   小于   |     IN(...)      |             在列表中多选一             |
|   >=   | 大于等于 |   LIEK  占位符   | 模糊匹配<br/>（%多个字符，_ 单个字符） |
|   <=   | 小于等于 |     IS NULL      |                  非空                  |
|   =    |   等于   |     AND 或 &     |                   且                   |
| <>或!= |  不等于  |    OR 或 \|\|    |                   或                   |
|        |          |    NOT 或 ！     |                   非                   |

> **聚合函数常和分组一起用**

| 函数  |   功能   |
| :---: | :------: |
| count | 统计数量 |
|  max  | 求最大值 |
|  min  | 求最小值 |
|  avg  | 求平均值 |
|  sum  |   求和   |

> **聚合函数不统计（计算）NULL值**

where和having的条件的区别：

1. where是在分组前对数据进行过滤，过滤的结果参与分组。
2. having是分组后再对结果进行过滤。
3. where不能对聚集函数进行判断。
4. having可以对聚集函数进行判断。

分页处理中limit后接上起始索引和每页多少条数据，起始索引是要查询的页数（从0开始）



<font color="red">**单表查询中语句执行顺序：**</font>

<font color="red">**from -> where -> group by -> having -> select -> order by -> limit**</font>



#### **2.多表查询：**

##### 2.1 表的关系

- 一对一：在任意一方加入外键，关联另一方主键，并且设置外键为唯一。
- 一对多：在多的一方建立外键，指向一的一方的主键。
- 多对多：建立第三张中间表，中间表至少包含两个外键，分别关联两房主键。



##### 2.2 连接查询--内连接

相当于查询A，B有字段数据重合的数据

```mysql
# 隐式内连接
select 字段列表 from 表1,表2 where 条件...;

# 显式内连接
select 字段列表 from 表1[inner] join 表2 on 连接条件...;
```



##### 2.3 连接查询--外连接

左外连接：查询左表所有数据，以及两张表交集部分数据。

右外连接：查询右表所有数据，以及两张表交集部分数据。

```mysql
# 左外连接
select 字段列表 from 表1 left [outer] join 表2 on 条件...;

# 右外连接
select 字段列表 from 表1 right [outer] join 表2 on 条件...;
```



##### 2.4 连接查询--自连接

当前表与自身进行连接查询，自连接必须使用别名。



##### 2.5 子查询

sql语句中嵌套select语句，称为嵌套查询，又称子查询。



#### 3. 查询语句执行顺序：

1. `FROM`
2. `ON`（连接条件）
3. `JOIN`
4. `WHERE`
5. `GROUP BY`（开始分组，产生分组结果）
6. `HAVING`（过滤分组后的结果）
7. `SELECT`
8. `DISTINCT`
9. `ORDER BY`（对最终结果排序）
10. `LIMIT`





## **3. 用户管理和权限**

```mysql
# 查询用户
use mysql;
select * from user;

# 创建用户
create user '用户名' @ '主机名' identified by '密码';

# 修改用户密码
alter user '用户名'@ '主机名' identified with mysql_native_password by '新密码';

# 删除用户
drop user '用户名'@ '主机名';
```

```mysql
# 查询权限
show grants for '用户名' @ '主机名';

# 授予权限
grant 权限列表 on 数据库.表名 to '用户名'@'主机名';

# 撤销权限
revoke 权限列表 on 数据库.表名 from '用户名'@'主机名';
```

权限列表：

|  权限  |          说明          |
| :----: | :--------------------: |
|  all   |        所有权限        |
| select |      查询数据权限      |
| insert |      插入数据权限      |
| update |      修改数据权限      |
| delete |      删除数据权限      |
| alter  |       修改表权限       |
|  drop  | 删除数据库/表/视图权限 |
| create |   创建数据库/表权限    |





## **4. 函数**

### 字符串函数：

| 函数名                                | 描述                                                         |
| :------------------------------------ | :----------------------------------------------------------- |
| ASCII(s)                              | 返回字符串 s 的第一个字符的 ASCII 码。                       |
| CHAR_LENGTH(s)                        | 返回字符串 s 的字符数                                        |
| CHARACTER_LENGTH(s)                   | 返回字符串 s 的字符数，等同于 CHAR_LENGTH(s)                 |
| CONCAT(s1,s2...sn)                    | 字符串 s1,s2 等多个字符串合并为一个字符串                    |
| CONCAT_WS(x,s1,s2...sn)               | 同 CONCAT(s1,s2,...) 函数，但是每个字符串之间要加上 x，x 可以是分隔符 |
| FIELD(s,s1,s2...)                     | 返回第一个字符串 s 在字符串列表(s1,s2...)中的位置            |
| FIND_IN_SET(s1,s2)                    | 返回在字符串s2中与s1匹配的字符串的位置                       |
| FORMAT(x,n)                           | 函数可以将数字 x 进行格式化 "#.##", 将 x 保留到小数点后 n 位，最后一位四舍五入。 |
| INSERT(s1,x,len,s2)                   | 字符串 s2 替换 s1 的 x 位置开始长度为 len 的字符串           |
| LOCATE(s1,s)                          | 从字符串 s 中获取 s1 的开始位置                              |
| LCASE(s)                              | 将字符串 s 的所有字母变成小写字母                            |
| LEFT(s,n)                             | 返回字符串 s 的前 n 个字符                                   |
| LOWER(s)                              | 将字符串 s 的所有字母变成小写字母                            |
| LPAD(s1,len,s2)                       | 在字符串 s1 的开始处填充字符串 s2，使字符串长度达到 len      |
| LTRIM(s)                              | 去掉字符串 s 开始处的空格                                    |
| MID(s,n,len)                          | 从字符串 s 的 n 位置截取长度为 len 的子字符串，同 SUBSTRING(s,n,len) |
| POSITION(s1 IN s)                     | 从字符串 s 中获取 s1 的开始位置                              |
| REPEAT(s,n)                           | 将字符串 s 重复 n 次                                         |
| REPLACE(s,s1,s2)                      | 将字符串 s2 替代字符串 s 中的字符串 s1                       |
| REVERSE(s)                            | 将字符串s的顺序反过来                                        |
| RIGHT(s,n)                            | 返回字符串 s 的后 n 个字符                                   |
| RPAD(s1,len,s2)                       | 在字符串 s1 的结尾处添加字符串 s2，使字符串的长度达到 len    |
| RTRIM(s)                              | 去掉字符串 s 结尾处的空格                                    |
| SPACE(n)                              | 返回 n 个空格                                                |
| STRCMP(s1,s2)                         | 比较字符串 s1 和 s2，如果 s1 与 s2 相等返回 0 ，如果 s1>s2 返回 1，如果 s1<s2 返回 -1 |
| SUBSTR(s, start, length)              | 从字符串 s 的 start 位置截取长度为 length 的子字符串         |
| SUBSTRING(s, start, length)           | 从字符串 s 的 start 位置截取长度为 length 的子字符串，等同于 SUBSTR(s, start, length) |
| SUBSTRING_INDEX(s, delimiter, number) | 返回从字符串 s 的第 number 个出现的分隔符 delimiter 之后的子串。 如果 number 是正数，返回第 number 个字符左边的字符串。 如果 number 是负数，返回第(number 的绝对值(从右边数))个字符右边的字符串。 |
| TRIM(s)                               | 去掉字符串 s 开始和结尾处的空格                              |
| UCASE(s)                              | 将字符串转换为大写                                           |
| UPPER(s)                              | 将字符串转换为大写                                           |



### 数学函数：

| 函数名                             | 描述                                                         |
| :--------------------------------- | :----------------------------------------------------------- |
| ABS(x)                             | 返回 x 的绝对值                                              |
| ACOS(x)                            | 求 x 的反余弦值（单位为弧度），x 为一个数值                  |
| ASIN(x)                            | 求反正弦值（单位为弧度），x 为一个数值                       |
| ATAN(x)                            | 求反正切值（单位为弧度），x 为一个数值                       |
| ATAN2(n, m)                        | 求反正切值（单位为弧度）                                     |
| AVG(expression)                    | 返回一个表达式的平均值，expression 是一个字段                |
| CEIL(x)                            | 向上取整                                                     |
| COS(x)                             | 求余弦值(参数是弧度)                                         |
| COT(x)                             | 求余切值(参数是弧度)                                         |
| COUNT(expression)                  | 返回查询的记录总数，expression 参数是一个字段或者 * 号       |
| DEGREES(x)                         | 将弧度转换为角度                                             |
| n DIV m                            | 整除，n 为被除数，m 为除数                                   |
| EXP(x)                             | 返回 e 的 x 次方                                             |
| FLOOR(x)                           | 向下取整                                                     |
| GREATEST(expr1, expr2, expr3, ...) | 返回列表中的最大值                                           |
| LEAST(expr1, expr2, expr3, ...)    | 返回列表中的最小值                                           |
| LN                                 | 返回数字的自然对数，以 e 为底。                              |
| LOG(x) 或 LOG(base, x)             | 返回自然对数(以 e 为底的对数)，如果带有 base 参数，则 base 为指定带底数。 |
| LOG10(x)                           | 返回以 10 为底的对数                                         |
| LOG2(x)                            | 返回以 2 为底的对数                                          |
| MAX(expression)                    | 返回字段 expression 中的最大值                               |
| MIN(expression)                    | 返回字段 expression 中的最小值                               |
| MOD(x,y)                           | 返回 x 除以 y 以后的余数                                     |
| PI()                               | 返回圆周率(3.141593）                                        |
| POW(x,y)                           | 返回 x 的 y 次方                                             |
| POWER(x,y)                         | 返回 x 的 y 次方                                             |
| RADIANS(x)                         | 将角度转换为弧度                                             |
| RAND()                             | 返回 0 到 1 的随机数                                         |
| ROUND(x [,y])                      | 返回离 x 最近的整数，可选参数 y 表示要四舍五入的小数位数，如果省略，则返回整数。 |
| SIGN(x)                            | 返回 x 的符号，x 是负数、0、正数分别返回 -1、0 和 1          |
| SIN(x)                             | 求正弦值(参数是弧度)                                         |
| SQRT(x)                            | 返回x的平方根                                                |
| SUM(expression)                    | 返回指定字段的总和                                           |
| TAN(x)                             | 求正切值(参数是弧度)                                         |
| TRUNCATE(x,y)                      | 返回数值 x 保留到小数点后 y 位的值（与 ROUND 最大的区别是不会进行四舍五入） |

### 日期函数：

| 函数名                                            | 描述                                                         |
| ------------------------------------------------- | ------------------------------------------------------------ |
| ADDDATE(d,n)                                      | 计算起始日期 d 加上 n 天的日期                               |
| ADDTIME(t,n)                                      | n 是一个时间表达式，时间 t 加上时间表达式 n                  |
| CURDATE()                                         | 返回当前日期                                                 |
| CURRENT_DATE()                                    | 返回当前日期                                                 |
| CURRENT_TIME                                      | 返回当前日期和时间                                           |
| CURRENT_TIMESTAMP()                               | 返回当前时间                                                 |
| CURTIME()                                         | 返回当前时间                                                 |
| DATE()                                            | 从日期或日期时间表达式中提取日期值                           |
| DATEDIFF(d1,d2)                                   | 计算日期 d1->d2 之间相隔的天数                               |
| DATE_ADD(d，INTERVAL expr type)                   | 计算起始日期 d 加上一个时间段后的日期                        |
| DATE_FORMAT(d,f)                                  | 按表达式 f的要求显示日期 d                                   |
| DATE_SUB(date,INTERVAL expr type)                 | 函数从日期减去指定的时间间隔。                               |
| DAY(d)                                            | 返回日期值 d 的日期部分                                      |
| DAYNAME(d)                                        | 返回日期 d 是星期几，如 Monday,Tuesday                       |
| DAYOFMONTH(d)                                     | 计算日期 d 是本月的第几天                                    |
| DAYOFWEEK(d)                                      | 日期 d 今天是星期几，1 星期日，2 星期一，以此类推            |
| DAYOFYEAR(d)                                      | 计算日期 d 是本年的第几天                                    |
| EXTRACT(type FROM d)                              | 从日期 d 中获取指定的值，type 指定返回的值。                 |
| FROM_DAYS(n)                                      | 计算从 0000 年 1 月 1 日开始 n 天后的日期                    |
| HOUR(t)                                           | 返回 t 中的小时值                                            |
| LAST_DAY(d)                                       | 返回给给定日期的那一月份的最后一天                           |
| LOCALTIME()                                       | 返回当前日期和时间                                           |
| LOCALTIMESTAMP()                                  | 返回当前日期和时间                                           |
| MAKEDATE(year, day-of-year)                       | 基于给定参数年份 year 和所在年中的天数序号 day-of-year 返回一个日期 |
| MAKETIME(hour, minute, second)                    | 组合时间，参数分别为小时、分钟、秒                           |
| MICROSECOND(date)                                 | 返回日期参数所对应的微秒数                                   |
| MINUTE(t)                                         | 返回 t 中的分钟值                                            |
| MONTHNAME(d)                                      | 返回日期当中的月份名称，如 November                          |
| MONTH(d)                                          | 返回日期d中的月份值，1 到 12                                 |
| NOW()                                             | 返回当前日期和时间                                           |
| PERIOD_ADD(period, number)                        | 为 年-月 组合日期添加一个时段                                |
| PERIOD_DIFF(period1, period2)                     | 返回两个时段之间的月份差值                                   |
| QUARTER(d)                                        | 返回日期d是第几季节，返回 1 到 4                             |
| SECOND(t)                                         | 返回 t 中的秒钟值                                            |
| SEC_TO_TIME(s)                                    | 将以秒为单位的时间 s 转换为时分秒的格式                      |
| STR_TO_DATE(string, format_mask)                  | 将字符串转变为日期                                           |
| SUBDATE(d,n)                                      | 日期 d 减去 n 天后的日期                                     |
| SUBTIME(t,n)                                      | 时间 t 减去 n 秒的时间                                       |
| SYSDATE()                                         | 返回当前日期和时间                                           |
| TIME(expression)                                  | 提取传入表达式的时间部分                                     |
| TIME_FORMAT(t,f)                                  | 按表达式 f 的要求显示时间 t                                  |
| TIME_TO_SEC(t)                                    | 将时间 t 转换为秒                                            |
| TIMEDIFF(time1, time2)                            | 计算时间差值                                                 |
| TIMESTAMP(expression, interval)                   | 单个参数时，函数返回日期或日期时间表达式；有2个参数时，将参数加和 |
| TIMESTAMPDIFF(unit,datetime_expr1,datetime_expr2) | 计算时间差，返回 datetime_expr2 − datetime_expr1 的时间差    |
| TO_DAYS(d)                                        | 计算日期 d 距离 0000 年 1 月 1 日的天数                      |
| WEEK(d)                                           | 计算日期 d 是本年的第几个星期，范围是 0 到 53                |
| WEEKDAY(d)                                        | 日期 d 是星期几，0 表示星期一，1 表示星期二                  |
| WEEKOFYEAR(d)                                     | 计算日期 d 是本年的第几个星期，范围是 0 到 53                |
| YEAR(d)                                           | 返回年份                                                     |
| YEARWEEK(date, mode)                              | 返回年份及第几周（0到53），mode 中 0 表示周天，1表示周一，以此类推 |

### 流程函数：

| 函数名                                                  | 描述                                             |
| ------------------------------------------------------- | :----------------------------------------------- |
| IF(value, t, f)                                         | 如果value为true，返回t，否则返回f                |
| IFNULL(value1, value2)                                  | 如果value不为空，返回value1，否则返回value2      |
| CASE WHEN [val1] THEN [res1] ... ELSE [default] END     | 如果val1为true，返回res1...否则返回default默认   |
| CASE[expr] WHEN [val1] THEN [res1]...ELSE [default] END | 如果expr的值等于val1，返回res1...否则返回default |

## **5. 约束**

![约束类型](https://ChengHaoRan666.github.io/picx-images-hosting/MySQL/约束类型.1aox312ajo.webp)



```mysql
# 创建表
create table 表名(列名1 数据类型 列级约束类型 [comment '注释'],
                 列名2 数据类型 列级约束类型  [comment '注释'],
                 列名3 数据类型 列级约束类型  [comment '注释'],
                表级约束
                 )comment ['注释'];
```

### 创建表时设置外键：

列级约束：

```mysql
# 例：
CREATE TABLE example (
  id INT PRIMARY KEY,  -- 列级的主键约束
  name VARCHAR(50) NOT NULL,  -- 列级的非空约束
  age INT DEFAULT 18,  -- 列级的默认值约束
  unique_column INT UNIQUE,  -- 列级的唯一约束
  sex INT,
  FOREIGN KEY (sex) REFERENCES another_table(another_id)  -- 列级的外键约束
);
```

表级约束：

```mysql
# 例：
CREATE TABLE example (
  id INT,
  name VARCHAR(50),
  age INT,
  unique_column1 INT,
  unique_column2 INT,
  PRIMARY KEY (id),  -- 表级的主键约束
  UNIQUE (unique_column1, unique_column2),  -- 表级的复合唯一约束
  FOREIGN KEY (name) REFERENCES another_table(name)  -- 表级的外键约束
);
```

### 创建表后添加删除外键：

```mysql
# 添加
alter table one_table add constraint 外键名字 foreign key (外键字段名) references 主表(主表字段名);

# 删除
alter table 表名 DROP FOREIGN KEY 外键名称;
```



## **6. 事务**

`事务`是一组操作的集合，是一个不可分割的工作单位，事务会把所有的操作作为一个整体一起向系统提交或者撤销操作请求，即这些操作要么`同时成功，要么同时失败`。

 

mysql中的事务是自动提交的。



### 事务操作：

#### 方式一：

设置手动提交后，操作只是临时修改，数据库中数据并没有改变，需要提交。

```mysql
  # 查看事务是否自动提交。1为自动提交，0为手动提交
set @@autocommit = 0; # 设置事务手动提交

commit; # 提交事务

rollback; # 回滚事务
```



#### 方式二：

```mysql
start transaction; # 开启事务
# 操作
commit; # 没有错误，提交
rollback; # 有错误，回滚
```





### 事务四大特性 ACID：

**原子性**：事务是不可分割的最小操作单元，要么全部成功，要么全部失败。

**一致性**：事务完成时，必须使所有的数据都保持一致状态。

**隔离性**：数据库系统提供的隔离机制，保证事务在不受外部并发操作影响的独立环境下运行。

**持久性**：事务一旦提交或回滚，它对数据库中的数据的改变就是永久的。



### 并发事务问题：

**脏读**：一个事务读到另外一个事务还没有提交的数据。

**不可重复读**：一个事务先后读取同一条记录，但两次读取的数据不同，称之为不可重复读。

**幻读**：一个事务按照条件查询数据时，没有对应的数据行，但是在插入数据时，又发现这行数据已经存在，好像出现了“幻影”。



### 事务的隔离级别：

==事务的隔离级别就是为了解决事务并发问题的。==

|       隔离级别        | 脏读 | 不可重复读 | 幻读 |
| :-------------------: | :--: | :--------: | :--: |
|   Read uncommitted    |  √   |     √      |  √   |
|    Read committed     |  ×   |     √      |  √   |
| Repeatable Read(默认) |  ×   |     ×      |  √   |
|     Serializable      |  ×   |     ×      |  ×   |

> 从上到下隔离级别越来越高，性能越来越低

```mysql
# 查看事务隔离级别
select @@transaction_isolation;

# session：对于当前客户端会话窗口有效
# global：所有所有客户端会话窗口有效
# 设置事务隔离级别
set session[global] transaction isolation level read uncommitted[...];
```
