## Linux安装MySQL

```shell
docker run \
--name mysql \
-v /home/www/mysql_data:/var/lib/mysql \
-p 3306:3306 \
-e MYSQL_ROOT_PASSWORD=chr@4521 \
--network blog  \
-d mysql:8.0.41 
```


