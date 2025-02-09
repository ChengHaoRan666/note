## 0. 安装

Ubuntu 22.04 64位安装Docker：

1. `curl -fsSL get.docker.com -o get-docker.sh`下载Docker官方安装脚本
2. `sudo sh get-docker.sh --mirror Aliyun`通过阿里云镜像安装Docker
3. `sudo systemctl enable --now docker`设置开机自启
4. `sudo systemctl status docker`检查运行状态
5. 配置镜像加速，参考网站：[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)
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
>
>
> 实例：docker run -d --name myNginx -p 88:80 --rm nginx

2. `docker ps` 查看运行容器列表

3. `docker ps -a` 查看所有容器列表

4. `docker stop 容器名字[容器ID]` 停止容器
5. `docker start 容器名字[容器ID]`  启动容器
6. `docker restart 容器名字[容器ID]` 重启容器
7. `docker stats 容器名字[容器ID]` 查看容器状态
8.  `docker logs 容器名字[容器ID]` 查看容器日志
9. `docker exec 容器名字[容器ID]` 进入容器
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

> 在创建容器时可以使用`--network 网络名`将容器加入网络中



