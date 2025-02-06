## 0. Docker安装

Ubuntu 22.04 64位安装Docker：

1. `curl -fsSL get.docker.com -o get-docker.sh`下载Docker官方安装脚本
2. `sudo sh get-docker.sh --mirror Aliyun`通过阿里云镜像安装Docker
3. `sudo systemctl enable --now docker`设置开机自启
4. `sudo systemctl status docker`检查运行状态
5. 配置镜像加速，参考网站：[阿里云镜像加速器](https://cr.console.aliyun.com/cn-hangzhou/instances/mirrors)
6. `sudo docker run hello-world`错测试Docker是否安装成功





## 1. Docker作用

> Docker可以快速方便启动，打包应用

> Docker有一个关键名词--`容器`。Docker的容器可以实现多个应用隔离

- 容器类似于轻量级的操作系统

- 容器共享操作系统内核

- 容器拥有自己的文件系统，CPU，内存，进程空间等





## 2. Docker常用命令

> #查看运行中的容器
> docker ps
> #查看所有容器
> docker ps -a
> #搜索镜像
> docker search nginx
> #下载镜像
> docker pull nginx
> #下载指定版本镜像
> docker pull nginx:1.26.0
> #查看所有镜像
> docker images
> #删除指定id的镜像
> docker rmi e784f4560448
>
>#运行一个新容器
> docker run nginx
> #停止容器
> docker stop keen_blackwell
> #启动容器
> docker start 592
> #重启容器
> docker restart 592
> #查看容器资源占用情况
> docker stats 592
> #查看容器日志
> docker logs 592
> #删除指定容器
> docker rm 592
> #强制删除指定容器
> docker rm -f 592
> #后台启动容器
> docker run -d --name mynginx nginx
> #后台启动并暴露端口
> docker run -d --name mynginx -p 80:80 nginx
> #进入容器内部
> docker exec -it mynginx /bin/bash
> 
>#提交容器变化打成一个新的镜像
> docker commit -m "update index.html" mynginx mynginx:v1.0
> #保存镜像为指定文件
> docker save -o mynginx.tar mynginx:v1.0
> #删除多个镜像
> docker rmi bde7d154a67f 94543a6c1aef e784f4560448
> #加载镜像
> docker load -i mynginx.tar 
> 
>#登录 docker hub
>docker login
> #重新给镜像打标签
> docker tag mynginx:v1.0 leifengyang/mynginx:v1.0
> #推送镜像
> docker push leifengyang/mynginx:v1.0