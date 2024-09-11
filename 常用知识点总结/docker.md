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
docker run --name mongo-dev -p 27017:27017 -e MONGO_INITDB_ROOT_USERNAME=admin -e MONGO_INITDB_ROOT_PASSWORD=123456Mongodb -d mongo
```
