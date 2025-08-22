## Linux安装redis

```shell
-- https://hub.docker.com/r/redis/redis-stack-server

docker run \
-p 6379:6379 \
--name redis-stack \
-v /home/www/redis/data:/data \
-e REDIS_ARGS="--requirepass mypassword" \
--network blog \
-d  redis/redis-stack-server
```

