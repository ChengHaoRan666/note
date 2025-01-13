```python
################################## 包含配置 ###################################
# 包含特定环境下配置文件
# include /path/to/local.conf
# include /path/to/other.conf
# include /path/to/fragments/*.conf

 
 ################################## 模块配置 #####################################
# 引入新的模块
# loadmodule /path/to/my_module.so
# loadmodule /path/to/other_module.so


################################## 网络配置 #####################################
# 配置监听的ip地址，不写默认全部监听
# bind 127.0.0.1 -::1


# 配置发起出站连接时的ip地址
# bind-source-addr 10.0.0.1


# 是否开启保护模式
protected-mode yes


# 默认监听端口
port 6379

# TCP监听队列长度
tcp-backlog 511


timeout 0


# TCP保活时间，默认300s
tcp-keepalive 300


################################# TLS/SSL #####################################
# 默认关闭TLS/SSL

# 要想启用SSL，设置port为0，tls-port为6379
# port 0
# tls-port 6379

# 设置服务器证书和私钥
# tls-cert-file redis.crt
# tls-key-file redis.key
# 如果私钥文件使用密码短语加密，可以在这里提供
# tls-key-file-pass secret

# Normally Redis uses the same certificate for both server functions (accepting
# connections) and client functions (replicating from a master, establishing
# cluster bus connections, etc.).


# 客户端证书
# tls-client-cert-file client.crt
# tls-client-key-file client.key

# Diffie-Hellman参数
# tls-dh-params-file redis.dh


# CA证书
# tls-ca-cert-file ca.crt
# tls-ca-cert-dir /etc/ssl/certs


# 客户端认证
# tls-auth-clients no
# tls-auth-clients optional


# 副本和集群的TLS配置
# tls-replication yes
# tls-cluster yes


# TLS版本和密码
# tls-protocols "TLSv1.2 TLSv1.3"
# tls-ciphers DEFAULT:!MEDIUM
# tls-ciphersuites TLS_CHACHA20_POLY1305_SHA256

# TLS会话缓存
# tls-session-caching no
# tls-session-cache-size 5000
# tls-session-cache-timeout 60



################################# 一般配置 #####################################
# 以守护进程的方式运行（后台运行）
daemonize yes

# supervised：这个配置选项允许Redis与系统服务管理器进行交互。可用的选项有：
# no：不与任何监督服务交互。
# upstart：通过将Redis置于SIGSTOP模式来与upstart交互，需要在upstart作业配置中包含"expect stop"。
# systemd：通过在启动时向$NOTIFY_SOCKET写入READY=1来与systemd交互，并在正常运行期间定期更新Redis状态。
# auto：根据环境变量UPSTART_JOB或NOTIFY_SOCKET自动检测是使用upstart还是systemd方法。
# 默认值是"no"。如果要在upstart或systemd下运行Redis，可以取消下面的注释行来启用。
# supervised no

# 生成的PID文件名
pidfile /var/run/redis_6379.pid

# 服务器日志等级：
# debug (记录大量信息，这对于开发和测试非常有用)
# verbose (记录许多信息，虽然这些信息很少被使用，但不会像调试级别那样混乱)
# notice (适度详细，这可能是生产环境中想要设置的级别，因为它提供了足够的信息来监控服务，但不会产生过多的日志)
# warning (只记录非常重要或关键的消息)
loglevel notice

# 设置日志文件名，如果为空则在控制台输出，如果选择以守护进程方式（daemonize yes）运行，则不会输出，在生产中建议设置日志文件名
logfile ""

# 是否启用将日志消息发送到系统日志（syslog）
# syslog-enabled no

# 在系统日志（syslog）的身份标识
# syslog-ident redis

# 在使用系统日志时使用哪个syslog设施（facility）
# syslog-facility local0

# 是否启用Redis内置的崩溃日志记录功能的设置
# crash-log-enabled no


# 是否启用Redis崩溃日志中的快速内存检查功能
# crash-memcheck-enabled no

# 设置redis数据库的数量
databases 16

# 是否在Redis启动时显示ASCII艺术字样的Logo
always-show-logo no

# 是否允许Redis修改进程标题，默认yes
set-proc-title yes

# 设置Redis在修改进程标题时使用的模板
# {title}           进程的原始名称，如果是父进程，则为执行的名称；如果是子进程，则为子进程的类型
# {listen-addr}     Redis监听的绑定地址，后面跟着TCP或TLS端口，或者如果只有Unix套接字可用，则为Unix套接字
# {server-mode}     特殊模式，例如"[sentinel]“或”[cluster]"
# {port}            Redis监听的TCP端口，如果没有监听则为0
# {tls-port}        Redis监听的TLS端口，如果没有监听则为0
# {unixsocket}      Redis监听的Unix域套接字，如果没有则为空字符串
# {config-file}     使用的配置文件名称
proc-title-template "{title} {listen-addr} {server-mode}"


################################ 持久化配置 RDB（默认） ################################
# 禁用RDB持久化
# save ""
# 默认情况：3600s进行1次修改 或 300s进行100次修改 或 60s 进行10000次修改
# save 3600 1 300 100 60 10000

# 当发生RDB保存文件失败后是否停止接收写操作，显式提醒用户，默认停止接收，显式提醒
stop-writes-on-bgsave-error yes

# 指定Redis将数据保存到.rdb文件时是否对字符串对象进行压缩
rdbcompression yes

# 是否在RDB文件末尾包含CRC64校验和，若采用redis-check-rdb指令检查rdb文件要求rdbchecksum为yes
rdbchecksum yes


# 在加载RDB文件或执行RESTORE命令时是否对ziplist、listpack等数据结构执行完整消毒检查
# no：从不执行完整消毒检查
# yes：总是执行完整消毒检查
# clients：只为用户连接执行完整消毒检查。这排除了从主连接接收的RDB文件、RESTORE命令，以及具有skip-sanitize-payload ACL标志的客户端连接
# sanitize-dump-payload no

# 设置保存的RDB文件名
dbfilename MyDump.rdb


# 在持久化（RDB和AOF）未启用的情况下，是否删除用于复制的RDB文件
rdb-del-sync-files no


# 设置Redis的工作目录,即数据库文件和追加日志文件（AOF文件）将被写入的目录
dir /opt/soft/redis-7.0.1/dumpfiles


################################# 主从复制 哨兵机制 #################################

# 主从复制时从节点配置主节点IP和端口号
# replicaof <masterIp> <masterPort>

# 从节点设置连接主节点密码（如果主节点设置密码的话）
# masterauth <master-password>

# 使用Redis ACLs（访问控制列表）时，为主从复制（replication）配置一个特殊的用户，让其具有主从复制权限
# masteruser <username>


# 当副本失去与主节点的连接或者在复制过程中，副本应该如何响应客户端的请求
# yes（默认）
# 副本仍然会响应客户端的请求，但可能会提供过时的数据，或者在第一次同步时数据集可能为空。
# 这种行为对于希望副本尽可能提供服务的场景是有用的，即使数据可能不是最新的。
# no
# 副本会对所有数据访问命令返回错误信息
# 这种行为适用于那些不希望客户端在副本与主节点断开连接时获取到可能过时数据的场景。
replica-serve-stale-data yes


# 设置从节点是否只支持读操作，默认从节点只可以读
replica-read-only yes


# 在主从复制过程中，全同步（full synchronization）策略的设置，即数据同步是通过磁盘还是直接通过套接字进行
# 
# 全同步策略
# 全同步是在以下情况下触发的：
# 新的副本节点加入时。
# 副本节点重新连接到主节点并且无法通过接收差异来继续复制过程时。
# 在全同步过程中，主节点的RDB文件会被传输到副本节点。
# 
# 传输方式
# 磁盘支持的复制（Disk-backed）：
# 主节点创建一个新进程，该进程将RDB文件写入磁盘。
# 然后父进程将文件逐渐传输给副本节点。
# 当RDB文件正在生成时，可以排队更多的副本节点，并在当前子进程完成RDB文件生成后立即为其提供服务。
# 
# 无磁盘复制（Diskless）：
# 主节点创建一个新进程，该进程直接将RDB文件写入到副本节点的套接字，完全不接触磁盘。
# 一旦传输开始，新到达的副本节点将被排队，当前传输结束后将开始新的传输。
# 当使用无磁盘复制时，主节点会在开始传输前等待一个可配置的时间（以秒为单位），希望有多个从节点到达，以便可以并行化传输。
# 
# repl-diskless-sync：这个配置指令用于指定是否使用无磁盘复制策略。如果设置为yes，则启用无磁盘复制（网络）；如果设置为no，则使用磁盘支持的复制。
repl-diskless-sync yes


# 在启用无磁盘复制时，服务器在启动子进程通过套接字将RDB文件传输到从节点之前等待的时间，默认5s
repl-diskless-sync-delay 5


# 在启用无磁盘复制时，在还未达到等待时间（repl-diskless-sync-delay）时，如果连接从节点数量达到多少就直接开始复制
# 默认为0，表示无上限，只有达到等待时间才开始复制
repl-diskless-sync-max-replicas 0


# 从节点在从主节点接收RDB文件时（无论是磁盘传输还是网络传输），是否直接从网络套接字加载RDB，还是先将其存储到磁盘上
# "disabled"：不使用无磁盘加载（先将rdb文件存储到磁盘）
# "on-empty-db"：仅在完全安全的情况下使用无磁盘加载
# "swapdb"：在直接从网络解析数据的同时，将当前数据库内容保留在内存中。在这种模式下，从节点可以在复制过程中继续服务当前数据集，除非它们无法识别主节点具有相同复制历史的数据集。
repl-diskless-load disabled


# 主节点向其从节点发送ping命令的预设时间间隔，确定从节点是否还在，默认10s
# repl-ping-replica-period 10


# 这个选项设置了复制过程中的几个超时时间：
# 从节点在主从复制期间进行批量传输I/O的超时时间
# 从节点视角的主节点超时时间（包括数据和PING命令）
# 主节点视角的从节点超时时间（针对REPLCONF ACK PING命令）
# 默认60s
# repl-timeout 60


# 这个配置选项用于控制Redis主从复制过程中，是否在从节点的套接字上禁用TCP_NODELAY
# 设置为"no"（默认值）：优化低延迟，数据会尽快发送到从节点，但可能会使用更多的带宽
# 设置为"yes"：优化带宽使用，可能会增加数据在从节点出现的延迟，最多可达到40毫秒（在默认配置的Linux内核上）
repl-disable-tcp-nodelay no


# 设置主节点复制积压缓冲区的大小
# 复制积压缓冲区是一个在主节点（master）上分配的内存缓冲区，用于存储最近发送到从节点（replicas）的数据
# 当从节点由于某些原因（如网络中断）与主节点断开连接时，积压缓冲区会继续累积主节点的写命令
# 当从节点重新连接时，它通常不需要进行完整的数据同步（full resync），而是可以通过部分同步（partial resync）来恢复，只需同步在断开连接期间错过的数据部分
# repl-backlog-size 1mb



# 设置主节点从最后一个从节点断开连接开始，需要经过多少秒后，复制积压缓冲区才会被释放
# repl-backlog-ttl 3600


# 设置从节点优先级数，用于在主节点挂时，低优先级数有利于提升为主节点
replica-priority 100

# 在主从复制或AOF文件恢复时出现错误的处理方式
# ignore：这是默认设置。如果设置为ignore，Redis在遇到无法在复制流中处理或从AOF文件中读取的命令时，将会忽略这些错误，并继续处理后续的命令。这样做的原因是，在Redis的早期版本中存在一些边缘情况，服务器可能会复制或持久化那些在未来版本中会失败的命令。忽略这些错误可以防止数据不一致，但可能会造成数据分歧。
# panic：如果设置为panic，Redis在遇到传播错误时将会停止所有操作。这可以确保不会出现数据分歧，因为服务器会在检测到潜在的数据一致性问题时代码直接崩溃。
# panic-on-replicas：如果设置为panic-on-replicas，则只有当从节点（replica）在复制流中遇到错误时，Redis服务器才会停止操作。主节点（master）在遇到相同的错误时不会停止。
# propagation-error-behavior ignore



# 从节点在无法将来自主节点的写命令持久化到磁盘时的处理方式
# no：这是默认设置。如果设置为no，当从节点无法将主节点的写命令持久化到磁盘时，从节点将崩溃。这是推荐的行为，因为它可以防止数据丢失或不一致。
# yes：如果设置为yes，当从节点遇到磁盘写入错误时，它将不会崩溃，而是记录一个警告日志，并继续执行从主节点接收到的写命令。这种行为与Redis的早期版本兼容，但它可能会导致数据丢失，因为即使写入失败，从节点也会继续处理后续的命令。
# replica-ignore-disk-write-errors no

# Redis 哨兵如何处理主从复制的公告
# yes：这是默认设置。如果设置为yes，哨兵会包括所有从节点在其公告中。这意味着所有从节点都会被sentinel replicas <master>命令报告，并且会被哨兵的客户端看到。
# no：如果设置为no，相应的从节点将不会出现在哨兵的公告中。这样的从节点将被sentinel replicas <master>命令忽略，并且不会被哨兵的客户端发现。
# replica-announced yes


# 配置主节点在特定条件下停止接受写操作，默认设置0个节点连接，不超过10s就继续接收写操作
# 定义了至少需要多少个从节点连接到主节点，并且这些副本的延迟时间不超过指定的秒数，主节点才会继续接受写操作
# min-replicas-to-write 3
# 定义了副本的最大延迟时间（以秒为单位），超过这个时间，副本就不会被认为是有效的，用于满足min-replicas-to-write的要求
# min-replicas-max-lag 10




# 配置从节点报告给主节点ip地址和端口号的信息，在NAT和端口转发情况下有用途
# replica-announce-ip 5.5.5.5
# replica-announce-port 1234


############################### 客户端缓存值 #################################
# 客户端缓存支持功能，它允许服务器帮助客户端缓存值，并在这些值在服务器端被修改时发送无效消息给客户端
# 定义客户端容纳最大键数
# tracking-table-max-keys 1000000




################################## 安全 ACLs访问控制列表的设置 ###################################
# 详情访问：https://redis.io/topics/acl
# 格式： user <username> ... acl rules ...
# 示例：user worker +@list +@connection ~jobs:* on >ffa9203c493aa99
# 启用或禁用用户（on/off）
# 允许或禁止执行特定命令（+<command>/-<command>）
# 允许或禁止执行命令类别（+@<category>/-@<category>）
# 设置可以访问的键的模式（~<pattern>）
# 设置可以读取或写入的键的模式（%R~<pattern>/%W~<pattern>）
# 设置可以访问的发布/订阅频道（&<pattern>）
# 添加或删除用户的密码（><password>/<<password>）
# 设置用户不需要密码（nopass）
# 重置用户密码、键模式和权限（resetpass/resetkeys/reset）




# 配置ACL日志的长度（单位字符）
acllog-max-len 128


# ACL允许外部ACL用户文件，但是配置文件和外部ACL用户文件不可重复，下面指令指定外部acl文件位置
# aclfile /etc/redis/users.acl


# 设置默认用户的密码
requirepass 123456



# 设置新用户对Pub/Sub通道的默认权限
# allchannels: 授予对所有Pub/Sub通道的访问权限。
# resetchannels: （默认）撤销对所有Pub/Sub通道的访问权限。
# acl-pubsub-default resetchannels



################################### 客户端数量 ####################################

# 设置最多连接客户端数量，默认10000
# maxclients 10000

############################## 内存管理 ################################

# 设置reids最多内存
# maxmemory <bytes>


# 设置redis超出最大内存时以哪种方式删除键释放内存
# volatile-lru -> 删除那些设置了过期时间（TTL）的键，优先删除最近最少使用（LRU）的键。适合用作缓存系统，其中一些键会设置过期时间，而未过期的键则不会被删除。
# allkeys-lru -> 从所有键中（无论是否有过期时间）删除最近最少使用（LRU）的键。适合需要灵活管理所有键的缓存场景。
# volatile-lfu -> 删除那些设置了过期时间（TTL）的键，优先删除使用频率最低（LFU）的键。LFU 更注重键的访问频率，适合缓存热点数据时使用。
# allkeys-lfu -> 从所有键中（无论是否有过期时间）删除使用频率最低（LFU）的键。
# volatile-random -> 随机删除设置了过期时间（TTL）的键。通常用于低优先级缓存，删除时无需关心访问模式或频率。
# allkeys-random -> 随机删除任意键（无论是否设置过期时间）。不常用，因为可能会删除重要的键。
# volatile-ttl -> 删除设置了过期时间（TTL）的键，并优先选择那些即将过期的键（TTL 最短）。对时间敏感的缓存场景可能有用。
# noeviction -> 不删除任何键。当内存达到上限时，直接返回错误，拒绝所有需要新增或扩展内存的操作。适合希望严格控制数据的持久性而非缓存的场景。
#
# maxmemory-policy noeviction


# redis在内存超出情况下清除内存时使用近似算法而不是精确算法来实现 LRU（最近最少使用）、LFU（最少使用频率）和最小 TTL 淘汰策略。
#  maxmemory-samples 指定 Redis 在每次选择要淘汰的键时，从多少个键中进行采样。
# 采样数量越大，结果越接近真实的 LRU/LFU/TLL 算法，但会消耗更多的 CPU。
# 采样数量越小，速度更快，但结果的准确性会下降。
# maxmemory-samples 5

# maxmemory-eviction-tenacity 用来调整 Redis 在执行键淘汰（eviction）时的强度（tenacity），即 Redis 为释放内存而投入的处理程度。
# 它主要控制 Redis 在响应客户端请求与执行内存淘汰任务之间的平衡。
# 取值0-100.0：着重客户端请求  100：着重内存淘汰任务
# maxmemory-eviction-tenacity 10

# 从节点从redis5开始就默认不进行内存管理，即maxmemory设置的值失效。
# 默认值yes，从节点不进行内存管理，只进行读操作，尽可能保证主从数据一致性。但是可能会造成从节点内存大小大于主节点内存大小（缓冲区开销）
# no：从节点进行内存管理，进行读写操作，可能会造成主从数据不一致情况
# replica-ignore-maxmemory yes

# redis的两种键过期处理方式：
# 1. 被动回收，当发生访问键时，redis检查键是否过期，过期删除
# 2. 主动回收，redis后台运行一个主动过期键扫描的任务，定期扫描已过期但还未访问的键
# active-expire-effort参数控制主动回收的“努力程度”，即扫描频率和深度。
# 参数范围1-10.1表示占用最少的CPU和内存，10表示扫描更加深入频繁
# active-expire-effort 1


############################# 阻塞删除，非阻塞删除 ####################################

# 删除key释放内存有两种方式：
# 1. 阻塞方式删除key：del 如果要删除的内容过大，可能会造成当前进程阻塞
# 2. 非阻塞方式删除key（惰性释放内存）：unlink 在其他进程执行删除操作，不会对主进程造成影响
# redis有时需要再用户未显式删除键的情况下删除键：内存不足，键过期，通过命令替换旧址，主从复制的全量同步，下面四个配置配置在这四种情况下能否非阻塞删除
# 在键的淘汰（内存容量超过）情况下是否采用非阻塞方式删除key
lazyfree-lazy-eviction no
# 在键过期的情况下是否采用非阻塞方式删除key
lazyfree-lazy-expire no
# 在服务器内部操作需要删除key时是否采用非阻塞方式删除key
lazyfree-lazy-server-del no
# 在主从复制时从节点是否采用非阻塞方式删除全部值
replica-lazy-flush no


# 进行用户的del操作转为unlink操作，阻塞方式转为非阻塞方式
# 当设置为yes时，用户的del操作全被转换为unlink操作
# 当设置为no时，用户的del操作不会转换
lazyfree-lazy-user-del no



# 设置FLUSHDB（删除当前数据库内容）、FLUSHALL（删除全部数据库内容）、SCRIPT FLUSH （删除脚本缓存）和 FUNCTION FLUSH（删除函数缓存）这四个命令是采用阻塞式删除还是非阻塞式删除
lazyfree-lazy-user-flush no


################################ I/O多线程 #################################
# 是否开启IO多线程，如果值为1，说明只有一个主线程，如果大于1，说明有多个线程处理IO操作
# io-threads 4
#
# 是否启动读操作多线程，默认不开启，读操作占用少，由主线程处理，写操作占用大，由其他线程处理
# io-threads-do-reads no


############################ Linux OOM killer机制干涉 ##############################

# 在极端环境下，redis可能会导致linux的内存超限，会触发Linux的OOM killer机制，将一些redis的进程杀死
# 通过oom-score-adj参数可以控制是否调整redis进程的oom-score-adj
# no：不修改oom-score-adj值，不干涉linux的OOM killer机制
# yes：等同于relative
# absolute：直接设置 oom_score_adj 值为配置的绝对值
# absolute：设置为相对于 Redis 启动时初始 oom_score_adj 的值，并限制在 -1000 到 1000 之间
oom-score-adj no

# 设置redis主实例，副本实例，后台子进程的oom-score-adj值
# 主实例：主要用于数据写入和读请求
# 副本实例：主要用于数据同步
# 后台子进程：用于保存快照RDB文件或重写AOF日志
# 下面例子设置主实例为0，最不容易被杀死，副本实例为200，中等可能性被杀死，后台子进程为800，最容易被杀死
oom-score-adj-values 0 200 800


#################### 是否禁用THP功能 ######################

# THP功能是 是 Linux 内核的一种内存管理功能，用于提高某些应用程序的性能。
# THP 将多个普通内存页（4 KB）合并成大页（通常为 2 MB），以减少页表开销，提高内存访问效率。
# 但是对于redis这中内存密集型应用而言，THP功能会导致redis性能下降，默认关闭THP功能
disable-thp yes

############################## 持久化配置 AOF ###############################
# 配置是否启用AOF持久化
appendonly no


# 在redis7版本后AOF文件存储形式发生变化，一份AOF数据由三部分组成：
# appendonly.aof.1.base.rdb   base基本文件，最多1个
# appendonly.aof.1.incr.aof      incr增量文件，可以多个
# appendonly.aof.manifest       manifest清单文件，会自动清除
# appendfilename参数设置了文件名
appendfilename "appendonly.aof"


# 设置AOF文件保存位置，由dir和apenddirname两个目录组成
appenddirname "aof_files"



# 三种写回策略：
# 写回是发生在缓冲区和AOF文件中，进行的命令会堆积在缓冲区中，如果达到指定大小，就进行写回到AOF文件中
# 控制AOF的精准程度
# 
# always：每次写入 AOF 日志文件后立即调用 fsync()。
# 优点：数据最安全，因为每次写操作都会立刻写入磁盘，最大限度地减少数据丢失风险。
# 缺点：性能最差，因为频繁调用 fsync() 会带来较高的 I/O 开销。
# 适用场景：对数据一致性要求极高的系统。
# 
# everysec (默认值)：每秒调用一次 fsync()。
# 优点：在性能与数据安全之间提供良好的平衡。即使系统崩溃，最多只会丢失 1 秒的数据。
# 缺点：比 always 稍微有可能丢失少量数据。
# 适用场景：大多数生产环境。
# 
# no：不主动调用 fsync()，仅依赖操作系统的缓冲区机制，何时将数据写入磁盘由操作系统决定。
# 优点：性能最佳，因为省去了 fsync() 的开销。
# 缺点：数据丢失风险较大，可能会丢失大量未刷新的数据。
# 适用场景：对性能要求极高，且可以接受数据丢失的场景。
# 
# appendfsync always
appendfsync everysec
# appendfsync no


# 
no-appendfsync-on-rewrite no

# Automatic rewrite of the append only file.
# Redis is able to automatically rewrite the log file implicitly calling
# BGREWRITEAOF when the AOF log size grows by the specified percentage.
#
# This is how it works: Redis remembers the size of the AOF file after the
# latest rewrite (if no rewrite has happened since the restart, the size of
# the AOF at startup is used).
#
# This base size is compared to the current size. If the current size is
# bigger than the specified percentage, the rewrite is triggered. Also
# you need to specify a minimal size for the AOF file to be rewritten, this
# is useful to avoid rewriting the AOF file even if the percentage increase
# is reached but it is still pretty small.
#
# Specify a percentage of zero in order to disable the automatic AOF
# rewrite feature.

auto-aof-rewrite-percentage 100
auto-aof-rewrite-min-size 64mb

# An AOF file may be found to be truncated at the end during the Redis
# startup process, when the AOF data gets loaded back into memory.
# This may happen when the system where Redis is running
# crashes, especially when an ext4 filesystem is mounted without the
# data=ordered option (however this can't happen when Redis itself
# crashes or aborts but the operating system still works correctly).
#
# Redis can either exit with an error when this happens, or load as much
# data as possible (the default now) and start if the AOF file is found
# to be truncated at the end. The following option controls this behavior.
#
# If aof-load-truncated is set to yes, a truncated AOF file is loaded and
# the Redis server starts emitting a log to inform the user of the event.
# Otherwise if the option is set to no, the server aborts with an error
# and refuses to start. When the option is set to no, the user requires
# to fix the AOF file using the "redis-check-aof" utility before to restart
# the server.
#
# Note that if the AOF file will be found to be corrupted in the middle
# the server will still exit with an error. This option only applies when
# Redis will try to read more data from the AOF file but not enough bytes
# will be found.
aof-load-truncated yes

# Redis can create append-only base files in either RDB or AOF formats. Using
# the RDB format is always faster and more efficient, and disabling it is only
# supported for backward compatibility purposes.
aof-use-rdb-preamble yes

# Redis supports recording timestamp annotations in the AOF to support restoring
# the data from a specific point-in-time. However, using this capability changes
# the AOF format in a way that may not be compatible with existing AOF parsers.
aof-timestamp-enabled no

################################ SHUTDOWN #####################################

# Maximum time to wait for replicas when shutting down, in seconds.
#
# During shut down, a grace period allows any lagging replicas to catch up with
# the latest replication offset before the master exists. This period can
# prevent data loss, especially for deployments without configured disk backups.
#
# The 'shutdown-timeout' value is the grace period's duration in seconds. It is
# only applicable when the instance has replicas. To disable the feature, set
# the value to 0.
#
# shutdown-timeout 10

# When Redis receives a SIGINT or SIGTERM, shutdown is initiated and by default
# an RDB snapshot is written to disk in a blocking operation if save points are configured.
# The options used on signaled shutdown can include the following values:
# default:  Saves RDB snapshot only if save points are configured.
#           Waits for lagging replicas to catch up.
# save:     Forces a DB saving operation even if no save points are configured.
# nosave:   Prevents DB saving operation even if one or more save points are configured.
# now:      Skips waiting for lagging replicas.
# force:    Ignores any errors that would normally prevent the server from exiting.
#
# Any combination of values is allowed as long as "save" and "nosave" are not set simultaneously.
# Example: "nosave force now"
#
# shutdown-on-sigint default
# shutdown-on-sigterm default

################ NON-DETERMINISTIC LONG BLOCKING COMMANDS #####################

# Maximum time in milliseconds for EVAL scripts, functions and in some cases
# modules' commands before Redis can start processing or rejecting other clients.
#
# If the maximum execution time is reached Redis will start to reply to most
# commands with a BUSY error.
#
# In this state Redis will only allow a handful of commands to be executed.
# For instance, SCRIPT KILL, FUNCTION KILL, SHUTDOWN NOSAVE and possibly some
# module specific 'allow-busy' commands.
#
# SCRIPT KILL and FUNCTION KILL will only be able to stop a script that did not
# yet call any write commands, so SHUTDOWN NOSAVE may be the only way to stop
# the server in the case a write command was already issued by the script when
# the user doesn't want to wait for the natural termination of the script.
#
# The default is 5 seconds. It is possible to set it to 0 or a negative value
# to disable this mechanism (uninterrupted execution). Note that in the past
# this config had a different name, which is now an alias, so both of these do
# the same:
# lua-time-limit 5000
# busy-reply-threshold 5000

################################ REDIS CLUSTER  ###############################

# Normal Redis instances can't be part of a Redis Cluster; only nodes that are
# started as cluster nodes can. In order to start a Redis instance as a
# cluster node enable the cluster support uncommenting the following:
#
# cluster-enabled yes

# Every cluster node has a cluster configuration file. This file is not
# intended to be edited by hand. It is created and updated by Redis nodes.
# Every Redis Cluster node requires a different cluster configuration file.
# Make sure that instances running in the same system do not have
# overlapping cluster configuration file names.
#
# cluster-config-file nodes-6379.conf

# Cluster node timeout is the amount of milliseconds a node must be unreachable
# for it to be considered in failure state.
# Most other internal time limits are a multiple of the node timeout.
#
# cluster-node-timeout 15000

# The cluster port is the port that the cluster bus will listen for inbound connections on. When set 
# to the default value, 0, it will be bound to the command port + 10000. Setting this value requires 
# you to specify the cluster bus port when executing cluster meet.
# cluster-port 0

# A replica of a failing master will avoid to start a failover if its data
# looks too old.
#
# There is no simple way for a replica to actually have an exact measure of
# its "data age", so the following two checks are performed:
#
# 1) If there are multiple replicas able to failover, they exchange messages
#    in order to try to give an advantage to the replica with the best
#    replication offset (more data from the master processed).
#    Replicas will try to get their rank by offset, and apply to the start
#    of the failover a delay proportional to their rank.
#
# 2) Every single replica computes the time of the last interaction with
#    its master. This can be the last ping or command received (if the master
#    is still in the "connected" state), or the time that elapsed since the
#    disconnection with the master (if the replication link is currently down).
#    If the last interaction is too old, the replica will not try to failover
#    at all.
#
# The point "2" can be tuned by user. Specifically a replica will not perform
# the failover if, since the last interaction with the master, the time
# elapsed is greater than:
#
#   (node-timeout * cluster-replica-validity-factor) + repl-ping-replica-period
#
# So for example if node-timeout is 30 seconds, and the cluster-replica-validity-factor
# is 10, and assuming a default repl-ping-replica-period of 10 seconds, the
# replica will not try to failover if it was not able to talk with the master
# for longer than 310 seconds.
#
# A large cluster-replica-validity-factor may allow replicas with too old data to failover
# a master, while a too small value may prevent the cluster from being able to
# elect a replica at all.
#
# For maximum availability, it is possible to set the cluster-replica-validity-factor
# to a value of 0, which means, that replicas will always try to failover the
# master regardless of the last time they interacted with the master.
# (However they'll always try to apply a delay proportional to their
# offset rank).
#
# Zero is the only value able to guarantee that when all the partitions heal
# the cluster will always be able to continue.
#
# cluster-replica-validity-factor 10

# Cluster replicas are able to migrate to orphaned masters, that are masters
# that are left without working replicas. This improves the cluster ability
# to resist to failures as otherwise an orphaned master can't be failed over
# in case of failure if it has no working replicas.
#
# Replicas migrate to orphaned masters only if there are still at least a
# given number of other working replicas for their old master. This number
# is the "migration barrier". A migration barrier of 1 means that a replica
# will migrate only if there is at least 1 other working replica for its master
# and so forth. It usually reflects the number of replicas you want for every
# master in your cluster.
#
# Default is 1 (replicas migrate only if their masters remain with at least
# one replica). To disable migration just set it to a very large value or
# set cluster-allow-replica-migration to 'no'.
# A value of 0 can be set but is useful only for debugging and dangerous
# in production.
#
# cluster-migration-barrier 1

# Turning off this option allows to use less automatic cluster configuration.
# It both disables migration to orphaned masters and migration from masters
# that became empty.
#
# Default is 'yes' (allow automatic migrations).
#
# cluster-allow-replica-migration yes

# By default Redis Cluster nodes stop accepting queries if they detect there
# is at least a hash slot uncovered (no available node is serving it).
# This way if the cluster is partially down (for example a range of hash slots
# are no longer covered) all the cluster becomes, eventually, unavailable.
# It automatically returns available as soon as all the slots are covered again.
#
# However sometimes you want the subset of the cluster which is working,
# to continue to accept queries for the part of the key space that is still
# covered. In order to do so, just set the cluster-require-full-coverage
# option to no.
#
# cluster-require-full-coverage yes

# This option, when set to yes, prevents replicas from trying to failover its
# master during master failures. However the replica can still perform a
# manual failover, if forced to do so.
#
# This is useful in different scenarios, especially in the case of multiple
# data center operations, where we want one side to never be promoted if not
# in the case of a total DC failure.
#
# cluster-replica-no-failover no

# This option, when set to yes, allows nodes to serve read traffic while the
# cluster is in a down state, as long as it believes it owns the slots.
#
# This is useful for two cases.  The first case is for when an application
# doesn't require consistency of data during node failures or network partitions.
# One example of this is a cache, where as long as the node has the data it
# should be able to serve it.
#
# The second use case is for configurations that don't meet the recommended
# three shards but want to enable cluster mode and scale later. A
# master outage in a 1 or 2 shard configuration causes a read/write outage to the
# entire cluster without this option set, with it set there is only a write outage.
# Without a quorum of masters, slot ownership will not change automatically.
#
# cluster-allow-reads-when-down no

# This option, when set to yes, allows nodes to serve pubsub shard traffic while
# the cluster is in a down state, as long as it believes it owns the slots.
#
# This is useful if the application would like to use the pubsub feature even when
# the cluster global stable state is not OK. If the application wants to make sure only
# one shard is serving a given channel, this feature should be kept as yes.
#
# cluster-allow-pubsubshard-when-down yes

# Cluster link send buffer limit is the limit on the memory usage of an individual
# cluster bus link's send buffer in bytes. Cluster links would be freed if they exceed
# this limit. This is to primarily prevent send buffers from growing unbounded on links
# toward slow peers (E.g. PubSub messages being piled up).
# This limit is disabled by default. Enable this limit when 'mem_cluster_links' INFO field
# and/or 'send-buffer-allocated' entries in the 'CLUSTER LINKS` command output continuously increase.
# Minimum limit of 1gb is recommended so that cluster link buffer can fit in at least a single
# PubSub message by default. (client-query-buffer-limit default value is 1gb)
#
# cluster-link-sendbuf-limit 0
 
# Clusters can configure their announced hostname using this config. This is a common use case for 
# applications that need to use TLS Server Name Indication (SNI) or dealing with DNS based
# routing. By default this value is only shown as additional metadata in the CLUSTER SLOTS
# command, but can be changed using 'cluster-preferred-endpoint-type' config. This value is 
# communicated along the clusterbus to all nodes, setting it to an empty string will remove 
# the hostname and also propagate the removal.
#
# cluster-announce-hostname ""

# Clusters can advertise how clients should connect to them using either their IP address,
# a user defined hostname, or by declaring they have no endpoint. Which endpoint is
# shown as the preferred endpoint is set by using the cluster-preferred-endpoint-type
# config with values 'ip', 'hostname', or 'unknown-endpoint'. This value controls how
# the endpoint returned for MOVED/ASKING requests as well as the first field of CLUSTER SLOTS. 
# If the preferred endpoint type is set to hostname, but no announced hostname is set, a '?' 
# will be returned instead.
#
# When a cluster advertises itself as having an unknown endpoint, it's indicating that
# the server doesn't know how clients can reach the cluster. This can happen in certain 
# networking situations where there are multiple possible routes to the node, and the 
# server doesn't know which one the client took. In this case, the server is expecting
# the client to reach out on the same endpoint it used for making the last request, but use
# the port provided in the response.
#
# cluster-preferred-endpoint-type ip

# In order to setup your cluster make sure to read the documentation
# available at https://redis.io web site.

########################## CLUSTER DOCKER/NAT support  ########################

# In certain deployments, Redis Cluster nodes address discovery fails, because
# addresses are NAT-ted or because ports are forwarded (the typical case is
# Docker and other containers).
#
# In order to make Redis Cluster working in such environments, a static
# configuration where each node knows its public address is needed. The
# following four options are used for this scope, and are:
#
# * cluster-announce-ip
# * cluster-announce-port
# * cluster-announce-tls-port
# * cluster-announce-bus-port
#
# Each instructs the node about its address, client ports (for connections
# without and with TLS) and cluster message bus port. The information is then
# published in the header of the bus packets so that other nodes will be able to
# correctly map the address of the node publishing the information.
#
# If cluster-tls is set to yes and cluster-announce-tls-port is omitted or set
# to zero, then cluster-announce-port refers to the TLS port. Note also that
# cluster-announce-tls-port has no effect if cluster-tls is set to no.
#
# If the above options are not used, the normal Redis Cluster auto-detection
# will be used instead.
#
# Note that when remapped, the bus port may not be at the fixed offset of
# clients port + 10000, so you can specify any port and bus-port depending
# on how they get remapped. If the bus-port is not set, a fixed offset of
# 10000 will be used as usual.
#
# Example:
#
# cluster-announce-ip 10.1.1.5
# cluster-announce-tls-port 6379
# cluster-announce-port 0
# cluster-announce-bus-port 6380

################################## SLOW LOG ###################################

# The Redis Slow Log is a system to log queries that exceeded a specified
# execution time. The execution time does not include the I/O operations
# like talking with the client, sending the reply and so forth,
# but just the time needed to actually execute the command (this is the only
# stage of command execution where the thread is blocked and can not serve
# other requests in the meantime).
#
# You can configure the slow log with two parameters: one tells Redis
# what is the execution time, in microseconds, to exceed in order for the
# command to get logged, and the other parameter is the length of the
# slow log. When a new command is logged the oldest one is removed from the
# queue of logged commands.

# The following time is expressed in microseconds, so 1000000 is equivalent
# to one second. Note that a negative number disables the slow log, while
# a value of zero forces the logging of every command.
slowlog-log-slower-than 10000

# There is no limit to this length. Just be aware that it will consume memory.
# You can reclaim memory used by the slow log with SLOWLOG RESET.
slowlog-max-len 128

################################ LATENCY MONITOR ##############################

# The Redis latency monitoring subsystem samples different operations
# at runtime in order to collect data related to possible sources of
# latency of a Redis instance.
#
# Via the LATENCY command this information is available to the user that can
# print graphs and obtain reports.
#
# The system only logs operations that were performed in a time equal or
# greater than the amount of milliseconds specified via the
# latency-monitor-threshold configuration directive. When its value is set
# to zero, the latency monitor is turned off.
#
# By default latency monitoring is disabled since it is mostly not needed
# if you don't have latency issues, and collecting data has a performance
# impact, that while very small, can be measured under big load. Latency
# monitoring can easily be enabled at runtime using the command
# "CONFIG SET latency-monitor-threshold <milliseconds>" if needed.
latency-monitor-threshold 0

################################ LATENCY TRACKING ##############################

# The Redis extended latency monitoring tracks the per command latencies and enables
# exporting the percentile distribution via the INFO latencystats command,
# and cumulative latency distributions (histograms) via the LATENCY command.
#
# By default, the extended latency monitoring is enabled since the overhead
# of keeping track of the command latency is very small.
# latency-tracking yes

# By default the exported latency percentiles via the INFO latencystats command
# are the p50, p99, and p999.
# latency-tracking-info-percentiles 50 99 99.9

############################# EVENT NOTIFICATION ##############################

# Redis can notify Pub/Sub clients about events happening in the key space.
# This feature is documented at https://redis.io/topics/notifications
#
# For instance if keyspace events notification is enabled, and a client
# performs a DEL operation on key "foo" stored in the Database 0, two
# messages will be published via Pub/Sub:
#
# PUBLISH __keyspace@0__:foo del
# PUBLISH __keyevent@0__:del foo
#
# It is possible to select the events that Redis will notify among a set
# of classes. Every class is identified by a single character:
#
#  K     Keyspace events, published with __keyspace@<db>__ prefix.
#  E     Keyevent events, published with __keyevent@<db>__ prefix.
#  g     Generic commands (non-type specific) like DEL, EXPIRE, RENAME, ...
#  $     String commands
#  l     List commands
#  s     Set commands
#  h     Hash commands
#  z     Sorted set commands
#  x     Expired events (events generated every time a key expires)
#  e     Evicted events (events generated when a key is evicted for maxmemory)
#  n     New key events (Note: not included in the 'A' class)
#  t     Stream commands
#  d     Module key type events
#  m     Key-miss events (Note: It is not included in the 'A' class)
#  A     Alias for g$lshzxetd, so that the "AKE" string means all the events
#        (Except key-miss events which are excluded from 'A' due to their
#         unique nature).
#
#  The "notify-keyspace-events" takes as argument a string that is composed
#  of zero or multiple characters. The empty string means that notifications
#  are disabled.
#
#  Example: to enable list and generic events, from the point of view of the
#           event name, use:
#
#  notify-keyspace-events Elg
#
#  Example 2: to get the stream of the expired keys subscribing to channel
#             name __keyevent@0__:expired use:
#
#  notify-keyspace-events Ex
#
#  By default all notifications are disabled because most users don't need
#  this feature and the feature has some overhead. Note that if you don't
#  specify at least one of K or E, no events will be delivered.
notify-keyspace-events ""

############################### ADVANCED CONFIG ###############################

# Hashes are encoded using a memory efficient data structure when they have a
# small number of entries, and the biggest entry does not exceed a given
# threshold. These thresholds can be configured using the following directives.
hash-max-listpack-entries 512
hash-max-listpack-value 64

# Lists are also encoded in a special way to save a lot of space.
# The number of entries allowed per internal list node can be specified
# as a fixed maximum size or a maximum number of elements.
# For a fixed maximum size, use -5 through -1, meaning:
# -5: max size: 64 Kb  <-- not recommended for normal workloads
# -4: max size: 32 Kb  <-- not recommended
# -3: max size: 16 Kb  <-- probably not recommended
# -2: max size: 8 Kb   <-- good
# -1: max size: 4 Kb   <-- good
# Positive numbers mean store up to _exactly_ that number of elements
# per list node.
# The highest performing option is usually -2 (8 Kb size) or -1 (4 Kb size),
# but if your use case is unique, adjust the settings as necessary.
list-max-listpack-size -2

# Lists may also be compressed.
# Compress depth is the number of quicklist ziplist nodes from *each* side of
# the list to *exclude* from compression.  The head and tail of the list
# are always uncompressed for fast push/pop operations.  Settings are:
# 0: disable all list compression
# 1: depth 1 means "don't start compressing until after 1 node into the list,
#    going from either the head or tail"
#    So: [head]->node->node->...->node->[tail]
#    [head], [tail] will always be uncompressed; inner nodes will compress.
# 2: [head]->[next]->node->node->...->node->[prev]->[tail]
#    2 here means: don't compress head or head->next or tail->prev or tail,
#    but compress all nodes between them.
# 3: [head]->[next]->[next]->node->node->...->node->[prev]->[prev]->[tail]
# etc.
list-compress-depth 0

# Sets have a special encoding in just one case: when a set is composed
# of just strings that happen to be integers in radix 10 in the range
# of 64 bit signed integers.
# The following configuration setting sets the limit in the size of the
# set in order to use this special memory saving encoding.
set-max-intset-entries 512

# Similarly to hashes and lists, sorted sets are also specially encoded in
# order to save a lot of space. This encoding is only used when the length and
# elements of a sorted set are below the following limits:
zset-max-listpack-entries 128
zset-max-listpack-value 64

# HyperLogLog sparse representation bytes limit. The limit includes the
# 16 bytes header. When an HyperLogLog using the sparse representation crosses
# this limit, it is converted into the dense representation.
#
# A value greater than 16000 is totally useless, since at that point the
# dense representation is more memory efficient.
#
# The suggested value is ~ 3000 in order to have the benefits of
# the space efficient encoding without slowing down too much PFADD,
# which is O(N) with the sparse encoding. The value can be raised to
# ~ 10000 when CPU is not a concern, but space is, and the data set is
# composed of many HyperLogLogs with cardinality in the 0 - 15000 range.
hll-sparse-max-bytes 3000

# Streams macro node max size / items. The stream data structure is a radix
# tree of big nodes that encode multiple items inside. Using this configuration
# it is possible to configure how big a single node can be in bytes, and the
# maximum number of items it may contain before switching to a new node when
# appending new stream entries. If any of the following settings are set to
# zero, the limit is ignored, so for instance it is possible to set just a
# max entries limit by setting max-bytes to 0 and max-entries to the desired
# value.
stream-node-max-bytes 4096
stream-node-max-entries 100

# Active rehashing uses 1 millisecond every 100 milliseconds of CPU time in
# order to help rehashing the main Redis hash table (the one mapping top-level
# keys to values). The hash table implementation Redis uses (see dict.c)
# performs a lazy rehashing: the more operation you run into a hash table
# that is rehashing, the more rehashing "steps" are performed, so if the
# server is idle the rehashing is never complete and some more memory is used
# by the hash table.
#
# The default is to use this millisecond 10 times every second in order to
# actively rehash the main dictionaries, freeing memory when possible.
#
# If unsure:
# use "activerehashing no" if you have hard latency requirements and it is
# not a good thing in your environment that Redis can reply from time to time
# to queries with 2 milliseconds delay.
#
# use "activerehashing yes" if you don't have such hard requirements but
# want to free memory asap when possible.
activerehashing yes

# The client output buffer limits can be used to force disconnection of clients
# that are not reading data from the server fast enough for some reason (a
# common reason is that a Pub/Sub client can't consume messages as fast as the
# publisher can produce them).
#
# The limit can be set differently for the three different classes of clients:
#
# normal -> normal clients including MONITOR clients
# replica -> replica clients
# pubsub -> clients subscribed to at least one pubsub channel or pattern
#
# The syntax of every client-output-buffer-limit directive is the following:
#
# client-output-buffer-limit <class> <hard limit> <soft limit> <soft seconds>
#
# A client is immediately disconnected once the hard limit is reached, or if
# the soft limit is reached and remains reached for the specified number of
# seconds (continuously).
# So for instance if the hard limit is 32 megabytes and the soft limit is
# 16 megabytes / 10 seconds, the client will get disconnected immediately
# if the size of the output buffers reach 32 megabytes, but will also get
# disconnected if the client reaches 16 megabytes and continuously overcomes
# the limit for 10 seconds.
#
# By default normal clients are not limited because they don't receive data
# without asking (in a push way), but just after a request, so only
# asynchronous clients may create a scenario where data is requested faster
# than it can read.
#
# Instead there is a default limit for pubsub and replica clients, since
# subscribers and replicas receive data in a push fashion.
#
# Note that it doesn't make sense to set the replica clients output buffer
# limit lower than the repl-backlog-size config (partial sync will succeed
# and then replica will get disconnected).
# Such a configuration is ignored (the size of repl-backlog-size will be used).
# This doesn't have memory consumption implications since the replica client
# will share the backlog buffers memory.
#
# Both the hard or the soft limit can be disabled by setting them to zero.
client-output-buffer-limit normal 0 0 0
client-output-buffer-limit replica 256mb 64mb 60
client-output-buffer-limit pubsub 32mb 8mb 60

# Client query buffers accumulate new commands. They are limited to a fixed
# amount by default in order to avoid that a protocol desynchronization (for
# instance due to a bug in the client) will lead to unbound memory usage in
# the query buffer. However you can configure it here if you have very special
# needs, such us huge multi/exec requests or alike.
#
# client-query-buffer-limit 1gb

# In some scenarios client connections can hog up memory leading to OOM
# errors or data eviction. To avoid this we can cap the accumulated memory
# used by all client connections (all pubsub and normal clients). Once we
# reach that limit connections will be dropped by the server freeing up
# memory. The server will attempt to drop the connections using the most 
# memory first. We call this mechanism "client eviction".
#
# Client eviction is configured using the maxmemory-clients setting as follows:
# 0 - client eviction is disabled (default)
#
# A memory value can be used for the client eviction threshold,
# for example:
# maxmemory-clients 1g
#
# A percentage value (between 1% and 100%) means the client eviction threshold
# is based on a percentage of the maxmemory setting. For example to set client
# eviction at 5% of maxmemory:
# maxmemory-clients 5%

# In the Redis protocol, bulk requests, that are, elements representing single
# strings, are normally limited to 512 mb. However you can change this limit
# here, but must be 1mb or greater
#
# proto-max-bulk-len 512mb

# Redis calls an internal function to perform many background tasks, like
# closing connections of clients in timeout, purging expired keys that are
# never requested, and so forth.
#
# Not all tasks are performed with the same frequency, but Redis checks for
# tasks to perform according to the specified "hz" value.
#
# By default "hz" is set to 10. Raising the value will use more CPU when
# Redis is idle, but at the same time will make Redis more responsive when
# there are many keys expiring at the same time, and timeouts may be
# handled with more precision.
#
# The range is between 1 and 500, however a value over 100 is usually not
# a good idea. Most users should use the default of 10 and raise this up to
# 100 only in environments where very low latency is required.
hz 10

# Normally it is useful to have an HZ value which is proportional to the
# number of clients connected. This is useful in order, for instance, to
# avoid too many clients are processed for each background task invocation
# in order to avoid latency spikes.
#
# Since the default HZ value by default is conservatively set to 10, Redis
# offers, and enables by default, the ability to use an adaptive HZ value
# which will temporarily raise when there are many connected clients.
#
# When dynamic HZ is enabled, the actual configured HZ will be used
# as a baseline, but multiples of the configured HZ value will be actually
# used as needed once more clients are connected. In this way an idle
# instance will use very little CPU time while a busy instance will be
# more responsive.
dynamic-hz yes

# When a child rewrites the AOF file, if the following option is enabled
# the file will be fsync-ed every 4 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
aof-rewrite-incremental-fsync yes

# When redis saves RDB file, if the following option is enabled
# the file will be fsync-ed every 4 MB of data generated. This is useful
# in order to commit the file to the disk more incrementally and avoid
# big latency spikes.
rdb-save-incremental-fsync yes

# Redis LFU eviction (see maxmemory setting) can be tuned. However it is a good
# idea to start with the default settings and only change them after investigating
# how to improve the performances and how the keys LFU change over time, which
# is possible to inspect via the OBJECT FREQ command.
#
# There are two tunable parameters in the Redis LFU implementation: the
# counter logarithm factor and the counter decay time. It is important to
# understand what the two parameters mean before changing them.
#
# The LFU counter is just 8 bits per key, it's maximum value is 255, so Redis
# uses a probabilistic increment with logarithmic behavior. Given the value
# of the old counter, when a key is accessed, the counter is incremented in
# this way:
#
# 1. A random number R between 0 and 1 is extracted.
# 2. A probability P is calculated as 1/(old_value*lfu_log_factor+1).
# 3. The counter is incremented only if R < P.
#
# The default lfu-log-factor is 10. This is a table of how the frequency
# counter changes with a different number of accesses with different
# logarithmic factors:
#
# +--------+------------+------------+------------+------------+------------+
# | factor | 100 hits   | 1000 hits  | 100K hits  | 1M hits    | 10M hits   |
# +--------+------------+------------+------------+------------+------------+
# | 0      | 104        | 255        | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 1      | 18         | 49         | 255        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 10     | 10         | 18         | 142        | 255        | 255        |
# +--------+------------+------------+------------+------------+------------+
# | 100    | 8          | 11         | 49         | 143        | 255        |
# +--------+------------+------------+------------+------------+------------+
#
# NOTE: The above table was obtained by running the following commands:
#
#   redis-benchmark -n 1000000 incr foo
#   redis-cli object freq foo
#
# NOTE 2: The counter initial value is 5 in order to give new objects a chance
# to accumulate hits.
#
# The counter decay time is the time, in minutes, that must elapse in order
# for the key counter to be divided by two (or decremented if it has a value
# less <= 10).
#
# The default value for the lfu-decay-time is 1. A special value of 0 means to
# decay the counter every time it happens to be scanned.
#
# lfu-log-factor 10
# lfu-decay-time 1

########################### ACTIVE DEFRAGMENTATION #######################
#
# What is active defragmentation?
# -------------------------------
#
# Active (online) defragmentation allows a Redis server to compact the
# spaces left between small allocations and deallocations of data in memory,
# thus allowing to reclaim back memory.
#
# Fragmentation is a natural process that happens with every allocator (but
# less so with Jemalloc, fortunately) and certain workloads. Normally a server
# restart is needed in order to lower the fragmentation, or at least to flush
# away all the data and create it again. However thanks to this feature
# implemented by Oran Agra for Redis 4.0 this process can happen at runtime
# in a "hot" way, while the server is running.
#
# Basically when the fragmentation is over a certain level (see the
# configuration options below) Redis will start to create new copies of the
# values in contiguous memory regions by exploiting certain specific Jemalloc
# features (in order to understand if an allocation is causing fragmentation
# and to allocate it in a better place), and at the same time, will release the
# old copies of the data. This process, repeated incrementally for all the keys
# will cause the fragmentation to drop back to normal values.
#
# Important things to understand:
#
# 1. This feature is disabled by default, and only works if you compiled Redis
#    to use the copy of Jemalloc we ship with the source code of Redis.
#    This is the default with Linux builds.
#
# 2. You never need to enable this feature if you don't have fragmentation
#    issues.
#
# 3. Once you experience fragmentation, you can enable this feature when
#    needed with the command "CONFIG SET activedefrag yes".
#
# The configuration parameters are able to fine tune the behavior of the
# defragmentation process. If you are not sure about what they mean it is
# a good idea to leave the defaults untouched.

# Active defragmentation is disabled by default
# activedefrag no

# Minimum amount of fragmentation waste to start active defrag
# active-defrag-ignore-bytes 100mb

# Minimum percentage of fragmentation to start active defrag
# active-defrag-threshold-lower 10

# Maximum percentage of fragmentation at which we use maximum effort
# active-defrag-threshold-upper 100

# Minimal effort for defrag in CPU percentage, to be used when the lower
# threshold is reached
# active-defrag-cycle-min 1

# Maximal effort for defrag in CPU percentage, to be used when the upper
# threshold is reached
# active-defrag-cycle-max 25

# Maximum number of set/hash/zset/list fields that will be processed from
# the main dictionary scan
# active-defrag-max-scan-fields 1000

# Jemalloc background thread for purging will be enabled by default
jemalloc-bg-thread yes

# It is possible to pin different threads and processes of Redis to specific
# CPUs in your system, in order to maximize the performances of the server.
# This is useful both in order to pin different Redis threads in different
# CPUs, but also in order to make sure that multiple Redis instances running
# in the same host will be pinned to different CPUs.
#
# Normally you can do this using the "taskset" command, however it is also
# possible to this via Redis configuration directly, both in Linux and FreeBSD.
#
# You can pin the server/IO threads, bio threads, aof rewrite child process, and
# the bgsave child process. The syntax to specify the cpu list is the same as
# the taskset command:
#
# Set redis server/io threads to cpu affinity 0,2,4,6:
# server_cpulist 0-7:2
#
# Set bio threads to cpu affinity 1,3:
# bio_cpulist 1,3
#
# Set aof rewrite child process to cpu affinity 8,9,10,11:
# aof_rewrite_cpulist 8-11
#
# Set bgsave child process to cpu affinity 1,10,11
# bgsave_cpulist 1,10-11

# In some cases redis will emit warnings and even refuse to start if it detects
# that the system is in bad state, it is possible to suppress these warnings
# by setting the following config which takes a space delimited list of warnings
# to suppress
#
# ignore-warnings ARM64-COW-BUG

```

