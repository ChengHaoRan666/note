## Linux安装MongoDB

```shell
docker run -d --name mongodb \
-v mongo_volume:/data/db \
-p 27017:27017 \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=secret \
mongo:latest
```

