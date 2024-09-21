# docker

## docker 启动mysql

```shell
docker run -d --name mysql-product -p 3306:3306 -e MYSQL_ROOT_PASSWORD=123456Mysql --restart=unless-stopped mysql
```

-d 后端执行
-e 设置环境变量
--restart=unless-stopped 一直重启，除非用户明确停止

## docker 启动mongodb

```shell
docker run --name mongo-dev -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=123456Mongodb --sysctl net.ipv4.tcp_keepalive_time=120 -d mongo
```

>--sysctl net.ipv4.tcp_keepalive_time=120 为了设置linux的keepalive时间，官方推荐120s，默认2小时。

## docker 容器宿主之间复制文件

```shell
docker cp 容器id:/path/to/file /path/to/destination
```

or

```shell
docker cp /path/to/destination 容器id:/path/to/file
```
