## 0. 安装

Ubuntu 22.04 64位安装Docker：

1. `curl -fsSL get.docker.com -o get-docker.sh`下载Docker官方安装脚本
2. `sudo sh get-docker.sh --mirror Aliyun`通过阿里云镜像安装Docker
3. `sudo systemctl enable --now docker`设置开机自启
4. `sudo systemctl status docker`检查运行状态
5. 配置镜像加速，参考网站：[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)

   > 其他加速方式：
   > https://github.com/DaoCloud/public-image-mirror/issues/2328
6. `sudo docker run hello-world` 测试Docker是否安装成功





## 1. 作用

> Docker可以快速方便启动，打包应用

> Docker有一个关键名词--`容器`。Docker的容器可以实现多个应用隔离

- 容器类似于轻量级的操作系统

- 容器共享操作系统内核

- 容器拥有自己的文件系统，CPU，内存，进程空间等





## 2. 常用命令

#### 镜像：

1. `docker search` 检索镜像
2. `docker pull 镜像名:版本` 下载镜像，不带版本默认下载最新版
3. `docker images` 查看镜像列表
4. `docker rmi 镜像id` 通过镜像id删除镜像
5. `docker rmi 镜像:版本` 通过镜像名和版本删除镜像

> 当镜像任被容器使用时不允许删除镜像，只能先删除容器，确保镜像没有被使用才可删除



#### 容器：

1. `docker run 镜像名`  创建并启动容器

> 可加参数：
>
> | 选项                   | 作用                           |
> | ---------------------- | ------------------------------ |
> | `-d`                   | 后台运行                       |
> | `--name`               | 设置容器名称                   |
> | `-p 外部端口:内部端口` | 设置容器端口映射               |
> | `--rm`                 | 退出后自动删除容器             |
> | `--restart=always`     | 容器崩溃或服务器重启后自动重启 |
> | `-e`                   | 设置环境变量                   |
>
>
> 实例：docker run -d --name myNginx -p 88:80 -e PASSWORD=123456 --rm nginx

2. `docker ps` 查看运行容器列表

3. `docker ps -a` 查看所有容器列表

4. `docker stop 容器名字[容器ID]` 停止容器
5. `docker start 容器名字[容器ID]`  启动容器
6. `docker restart 容器名字[容器ID]` 重启容器
7. `docker stats 容器名字[容器ID]` 查看容器状态
8.  `docker logs 容器名字[容器ID]` 查看容器日志
9. `docker exec 容器名字[容器ID] sh/bash` 进入容器
10. `docker rm 容器名字[容器ID]` 删除容器



#### 保存镜像：

1. `docker commit [OPTIONS] <容器ID或容器名> <新镜像名>:<标签>`将修改后的容器保存为新的镜像

   > 可加参数：
   >
   > | 选项                              | 作用                             |
   > | --------------------------------- | -------------------------------- |
   > | `-a, --author "<作者信息>"`       | 记录镜像作者                     |
   > | `-m, --message "<提交信息>"`      | 添加提交说明                     |
   > | `-c, --change "<Dockerfile命令>"` | 直接修改镜像配置                 |
   > | `--pause / --no-pause`            | 是否在提交前暂停容器（默认暂停） |

2. `docker save -o <保存的归档文件名> <镜像名:标签>`将镜像保存为tar文件

3. `docker load -i name.tar` 加载镜像



## 3. 存储

在使用`docker run`命令创建并启动一个docker容器时可以使用`-v`进行挂载或映射。

- 目录挂载： `-v /app/nghtml:/usr/share/nginx/html`
- 卷映射：`-v ngconf:/etc/nginx`

> 目录挂载：在外部创建一个文件夹或者文件（如果没有的话），使用它代替容器内部的那个文件夹或者文件。
>
> 卷映射：在外部创建一个文件夹或者文件，内容和容器内对应的内容一致。用外部的代替内部的文件夹或者文件。

> 卷映射docker统一存放在一个位置：/var/lib/docker/volumes/<volume-name\>

1. `docker volume ls`可以查看所有卷
2. `docker volume create 卷名`手动创建卷
3. `docker volume inspect 卷名`查看卷的详细信息
3. `docker volume rm 卷名`删除卷



## 4. 网络

#### 方法一：ip访问

容器之间进行网络通讯，可以直接在机器内部进行通讯。docker为每个容器分配了唯一ip，使用容器ip+容器端口可以相互访问。



#### 方法二：域名访问

使用ip访问存在一定问题：如果容器有变化那么无法正确访问。可以采用域名访问方式。

docker0默认不支持主机域名，需要自己创建自定义网络，容器名就是稳定域名。

1. `docker network ls` 查看docker网络
2. `docker network create <网络名>`创建docker网络
3. `docker network connect <网络名> <容器名或ID>`将容器加入到这个网络中
4. `docker network disconnect <网络名> <容器名或ID>`断开容器网络
4. `docker network inspect <网络名>`查看网络详细状态

> 在创建容器时可以使用`--network 网络名`将容器加入网络中



## 5. Docker Compose

Docker Compose 是一个用于定义和管理 多容器应用 的工具。它允许你使用 YAML 配置文件`docker-compose.yml`来定义多个服务，并通过一条命令启动所有容器。

[参考文档](https://docs.docker.com/reference/compose-file/)

#### 指令：

1. `docker-compose up -d`启动服务

   > 可以用-f参数指定配置文件，不指定默认docker-compose.yaml

2. `docker-compose ps`查看运行的容器

3. `docker-compose down`停止和删除容器

4. `docker-compose restart`重新启动服务

5. `docker-compose logs -f`访问服务日志

6. `docker-compose exec <服务名> bash`进入某个容器



#### 语法：

顶级元素：

- name：名字
- services：服务
- networks：网络
- volumes：卷
- configs：配置
- secrets：秘钥



 compose.yaml

```yaml
name: myblog
services:
  mysql:
    container_name: mysql
    image: mysql:8.0
    ports:
      - "3306:3306"
    environment:
      - MYSQL_ROOT_PASSWORD=123456
      - MYSQL_DATABASE=wordpress
    volumes:
      - mysql-data:/var/lib/mysql
      - /app/myconf:/etc/mysql/conf.d
    restart: always
    networks:
      - blog

  wordpress:
    image: wordpress
    ports:
      - "8080:80"
    environment:
      WORDPRESS_DB_HOST: mysql
      WORDPRESS_DB_USER: root
      WORDPRESS_DB_PASSWORD: 123456
      WORDPRESS_DB_NAME: wordpress
    volumes:
      - wordpress:/var/www/html
    restart: always
    networks:
      - blog
    depends_on:
      - mysql

volumes:
  mysql-data:
  wordpress:

networks:
  blog:
```



## 6. Dockerfile 

Dockerfile 是一个用于定义 Docker 镜像的文本文件，包含一系列指令（如 FROM、RUN、COPY 等），用于自动化创建 Docker 容器环境。  

Dockerfile 主要作用 ：

- 自动化构建镜像：通过 `docker build` 命令，根据 Dockerfile 创建自定义镜像。 
- 保证环境一致性：开发、测试、生产环境使用相同的容器镜像，避免“本地可以运行，服务器不能运行”的问题。 
- 提升部署效率：通过 Docker Hub 或私有仓库共享镜像，实现快速部署。

|  常见指令  |        作用        |
| :--------: | :----------------: |
|    FROM    |  指定镜像基础环境  |
|    RUN     |   运行自定义命令   |
|    CMD     | 容器启动命令或参数 |
|   LABEL    |     自定义标签     |
|   EXPOSE   |    指定暴露端口    |
|    ENV     |      环境变量      |
|    ADD     |   添加文件到镜像   |
|    COPY    |   复制文件到镜像   |
| ENTRYPOINT |  容器固定启动命令  |
|   VOLUME   |       数据卷       |
|    USER    |  指定用户和用户组  |
|  WORKDIR   |  指定默认工作目录  |
|    ARG     |    指定构建参数    |

```shell
FROM openjdk:17

LABEL author=leifengyang

COPY app.jar /app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```



创建好dockerfile文件写好内容后，用
`docker build -f dockerfile -t myjavaapp:v1.0 .`构建镜像

> 有jar包之后，使用dockerfile构建镜像可以分享。

 



## 7. 一键启动中间件

```shell
# 先执行
#Disable memory paging and swapping performance
sudo swapoff -a

# Edit the sysctl config file
sudo vi /etc/sysctl.conf

# Add a line to define the desired value
# or change the value if the key exists,
# and then save your changes.
vm.max_map_count=262144

# Reload the kernel parameters using sysctl
sudo sysctl -p

# Verify that the change was applied by checking the value
cat /proc/sys/vm/max_map_count
```

```yaml
# compose.yaml
name: devsoft
services:
  redis:
    image: bitnami/redis:latest
    restart: always
    container_name: redis
    environment:
      - REDIS_PASSWORD=123456
    ports:
      - '6379:6379'
    volumes:
      - redis-data:/bitnami/redis/data
      - redis-conf:/opt/bitnami/redis/mounted-etc
      - /etc/localtime:/etc/localtime:ro

  mysql:
    image: mysql:8.0.41
    restart: always
    container_name: mysql
    environment:
      - MYSQL_ROOT_PASSWORD=123456
    ports:
      - '3306:3306'
      - '33060:33060'
    volumes:
      - mysql-conf:/etc/mysql/conf.d
      - mysql-data:/var/lib/mysql
      - /etc/localtime:/etc/localtime:ro

  rabbit:
    image: rabbitmq:3-management
    restart: always
    container_name: rabbitmq
    ports:
      - "5672:5672"
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=rabbit
      - RABBITMQ_DEFAULT_PASS=rabbit
      - RABBITMQ_DEFAULT_VHOST=dev
    volumes:
      - rabbit-data:/var/lib/rabbitmq
      - rabbit-app:/etc/rabbitmq
      - /etc/localtime:/etc/localtime:ro
  opensearch-node1:
    image: opensearchproject/opensearch:2.13.0
    container_name: opensearch-node1
    environment:
      - cluster.name=opensearch-cluster # Name the cluster
      - node.name=opensearch-node1 # Name the node that will run in this container
      - discovery.seed_hosts=opensearch-node1,opensearch-node2 # Nodes to look for when discovering the cluster
      - cluster.initial_cluster_manager_nodes=opensearch-node1,opensearch-node2 # Nodes eligibile to serve as cluster manager
      - bootstrap.memory_lock=true # Disable JVM heap memory swapping
      - "OPENSEARCH_JAVA_OPTS=-Xms512m -Xmx512m" # Set min and max JVM heap sizes to at least 50% of system RAM
      - "DISABLE_INSTALL_DEMO_CONFIG=true" # Prevents execution of bundled demo script which installs demo certificates and security configurations to OpenSearch
      - "DISABLE_SECURITY_PLUGIN=true" # Disables Security plugin
    ulimits:
      memlock:
        soft: -1 # Set memlock to unlimited (no soft or hard limit)
        hard: -1
      nofile:
        soft: 65536 # Maximum number of open files for the opensearch user - set to at least 65536
        hard: 65536
    volumes:
      - opensearch-data1:/usr/share/opensearch/data # Creates volume called opensearch-data1 and mounts it to the container
      - /etc/localtime:/etc/localtime:ro
    ports:
      - 9200:9200 # REST API
      - 9600:9600 # Performance Analyzer

  opensearch-node2:
    image: opensearchproject/opensearch:2.13.0
    container_name: opensearch-node2
    environment:
      - cluster.name=opensearch-cluster # Name the cluster
      - node.name=opensearch-node2 # Name the node that will run in this container
      - discovery.seed_hosts=opensearch-node1,opensearch-node2 # Nodes to look for when discovering the cluster
      - cluster.initial_cluster_manager_nodes=opensearch-node1,opensearch-node2 # Nodes eligibile to serve as cluster manager
      - bootstrap.memory_lock=true # Disable JVM heap memory swapping
      - "OPENSEARCH_JAVA_OPTS=-Xms512m -Xmx512m" # Set min and max JVM heap sizes to at least 50% of system RAM
      - "DISABLE_INSTALL_DEMO_CONFIG=true" # Prevents execution of bundled demo script which installs demo certificates and security configurations to OpenSearch
      - "DISABLE_SECURITY_PLUGIN=true" # Disables Security plugin
    ulimits:
      memlock:
        soft: -1 # Set memlock to unlimited (no soft or hard limit)
        hard: -1
      nofile:
        soft: 65536 # Maximum number of open files for the opensearch user - set to at least 65536
        hard: 65536
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - opensearch-data2:/usr/share/opensearch/data # Creates volume called opensearch-data2 and mounts it to the container

  opensearch-dashboards:
    image: opensearchproject/opensearch-dashboards:2.13.0
    container_name: opensearch-dashboards
    ports:
      - 5601:5601 # Map host port 5601 to container port 5601
    expose:
      - "5601" # Expose port 5601 for web access to OpenSearch Dashboards
    environment:
      - 'OPENSEARCH_HOSTS=["http://opensearch-node1:9200","http://opensearch-node2:9200"]'
      - "DISABLE_SECURITY_DASHBOARDS_PLUGIN=true" # disables security dashboards plugin in OpenSearch Dashboards
    volumes:
      - /etc/localtime:/etc/localtime:ro
  zookeeper:
    image: bitnami/zookeeper:3.9
    container_name: zookeeper
    restart: always
    ports:
      - "2181:2181"
    volumes:
      - "zookeeper_data:/bitnami"
      - /etc/localtime:/etc/localtime:ro
    environment:
      - ALLOW_ANONYMOUS_LOGIN=yes

  kafka:
    image: 'bitnami/kafka:3.4'
    container_name: kafka
    restart: always
    hostname: kafka
    ports:
      - '9092:9092'
      - '9094:9094'
    environment:
      - KAFKA_CFG_NODE_ID=0
      - KAFKA_CFG_PROCESS_ROLES=controller,broker
      - KAFKA_CFG_LISTENERS=PLAINTEXT://:9092,CONTROLLER://:9093,EXTERNAL://0.0.0.0:9094
      - KAFKA_CFG_ADVERTISED_LISTENERS=PLAINTEXT://kafka:9092,EXTERNAL://119.45.147.122:9094
      - KAFKA_CFG_LISTENER_SECURITY_PROTOCOL_MAP=CONTROLLER:PLAINTEXT,EXTERNAL:PLAINTEXT,PLAINTEXT:PLAINTEXT
      - KAFKA_CFG_CONTROLLER_QUORUM_VOTERS=0@kafka:9093
      - KAFKA_CFG_CONTROLLER_LISTENER_NAMES=CONTROLLER
      - ALLOW_PLAINTEXT_LISTENER=yes
      - "KAFKA_HEAP_OPTS=-Xmx512m -Xms512m"
    volumes:
      - kafka-conf:/bitnami/kafka/config
      - kafka-data:/bitnami/kafka/data
      - /etc/localtime:/etc/localtime:ro
  kafka-ui:
    container_name: kafka-ui
    image: provectuslabs/kafka-ui:latest
    restart: always
    ports:
      - 8080:8080
    environment:
      DYNAMIC_CONFIG_ENABLED: true
      KAFKA_CLUSTERS_0_NAME: kafka-dev
      KAFKA_CLUSTERS_0_BOOTSTRAPSERVERS: kafka:9092
    volumes:
      - kafkaui-app:/etc/kafkaui
      - /etc/localtime:/etc/localtime:ro

  nacos:
    image: nacos/nacos-server:v2.3.1
    container_name: nacos
    ports:
      - 8848:8848
      - 9848:9848
    environment:
      - PREFER_HOST_MODE=hostname
      - MODE=standalone
      - JVM_XMX=512m
      - JVM_XMS=512m
      - SPRING_DATASOURCE_PLATFORM=mysql
      - MYSQL_SERVICE_HOST=nacos-mysql
      - MYSQL_SERVICE_DB_NAME=nacos_devtest
      - MYSQL_SERVICE_PORT=3306
      - MYSQL_SERVICE_USER=nacos
      - MYSQL_SERVICE_PASSWORD=nacos
      - MYSQL_SERVICE_DB_PARAM=characterEncoding=utf8&connectTimeout=1000&socketTimeout=3000&autoReconnect=true&useUnicode=true&useSSL=false&serverTimezone=Asia/Shanghai&allowPublicKeyRetrieval=true
      - NACOS_AUTH_IDENTITY_KEY=2222
      - NACOS_AUTH_IDENTITY_VALUE=2xxx
      - NACOS_AUTH_TOKEN=SecretKey012345678901234567890123456789012345678901234567890123456789
      - NACOS_AUTH_ENABLE=true
    volumes:
      - /app/nacos/standalone-logs/:/home/nacos/logs
      - /etc/localtime:/etc/localtime:ro
    depends_on:
      nacos-mysql:
        condition: service_healthy
  nacos-mysql:
    container_name: nacos-mysql
    build:
      context: .
      dockerfile_inline: |
        FROM mysql:8.0.31
        ADD https://raw.githubusercontent.com/alibaba/nacos/2.3.2/distribution/conf/mysql-schema.sql /docker-entrypoint-initdb.d/nacos-mysql.sql
        RUN chown -R mysql:mysql /docker-entrypoint-initdb.d/nacos-mysql.sql
        EXPOSE 3306
        CMD ["mysqld", "--character-set-server=utf8mb4", "--collation-server=utf8mb4_unicode_ci"]
    image: nacos/mysql:8.0.30
    environment:
      - MYSQL_ROOT_PASSWORD=root
      - MYSQL_DATABASE=nacos_devtest
      - MYSQL_USER=nacos
      - MYSQL_PASSWORD=nacos
      - LANG=C.UTF-8
    volumes:
      - nacos-mysqldata:/var/lib/mysql
      - /etc/localtime:/etc/localtime:ro
    ports:
      - "13306:3306"
    healthcheck:
      test: [ "CMD", "mysqladmin" ,"ping", "-h", "localhost" ]
      interval: 5s
      timeout: 10s
      retries: 10
  prometheus:
    image: prom/prometheus:v2.52.0
    container_name: prometheus
    restart: always
    ports:
      - 9090:9090
    volumes:
      - prometheus-data:/prometheus
      - prometheus-conf:/etc/prometheus
      - /etc/localtime:/etc/localtime:ro

  grafana:
    image: grafana/grafana:10.4.2
    container_name: grafana
    restart: always
    ports:
      - 3000:3000
    volumes:
      - grafana-data:/var/lib/grafana
      - /etc/localtime:/etc/localtime:ro

volumes:
  redis-data:
  redis-conf:
  mysql-conf:
  mysql-data:
  rabbit-data:
  rabbit-app:
  opensearch-data1:
  opensearch-data2:
  nacos-mysqldata:
  zookeeper_data:
  kafka-conf:
  kafka-data:
  kafkaui-app:
  prometheus-data:
  prometheus-conf:
  grafana-data:
```





## 八. 实践

```shell
docker run --name mysql -v /home/www/mysql_data:/var/lib/mysql -p 3306:3306 -e MYSQL_ROOT_PASSWORD=chr@4521 --network blog  -d mysql:8.0.41 
```

```shell
docker run \
> -p 80:80 \
> --name blog_jar \
> --network blog \
> -d blog
```

