# docker-compose

启动

```shell
docker-compose up -d
```

`-d` 表示在后台运行

停止并移除所有服务

```shell
docker-compose down
```

停止，移除服务，并删除镜像

```shell
docker-compose down --rmi all
```

停止重新构建镜像，即使镜像已经存在

`--build`

```shell
docker-compose up --build -d
```

