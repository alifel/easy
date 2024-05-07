# docker image prune

|description|remove one or more images|
|---|---|
|**Usage**|`docker image prune [OPTIONS]`|

## Description

Remove all dangling images. If `-a` is specified, also remove all images not referenced by any container.

移除素有danglin(:pill:没有被tag的)镜像。如果`-a`被指定，则同时删除所有未被任何容器引用的镜像。

## Options

|option|Default|Description|
|---|---|---|
|`-a, --all`||Remove all unused images, not just dangling ones|
|`--filter`||Provide filter values (e.g. [`until=<timestamp>`](#filter)[【?】](#filter))|
|`-f, --force`||Do not prompt for confirmation|

|选项|默认值|描述|
|---|---|---|
|`-a, --all`||删除所有未使用的镜像，不仅仅是dangling(:pill:没有被tag的)的|
|`--filter`||Provide filter values (e.g. [`until=<timestamp>`](#filter)[【?】](#filter))|
|`-f, --force`||Do not prompt for confirmation|

## Examples

Example output:

```sh
$ docker image prune -a

WARNING! This will remove all images without at least one container associated to them.
Are you sure you want to continue? [y/N] y
Deleted Images:
untagged: alpine:latest
untagged: alpine@sha256:3dcdb92d7432d56604d4545cbd324b14e647b313626d99b889d0626de158f73a
deleted: sha256:4e38e38c8ce0b8d9041a9c4fefe786631d1416225e13b0bfe8cfa2321aec4bba
deleted: sha256:4fe15f8d0ae69e169824f25f1d4da3015a48feeeeebb265cd2e328e15c6a869f
untagged: alpine:3.3
untagged: alpine@sha256:4fa633f4feff6a8f02acfc7424efd5cb3e76686ed3218abf4ca0fa4a2a358423
untagged: my-jq:latest
deleted: sha256:ae67841be6d008a374eff7c2a974cde3934ffe9536a7dc7ce589585eddd83aff
deleted: sha256:34f6f1261650bc341eb122313372adc4512b4fceddc2a7ecbb84f0958ce5ad65
deleted: sha256:cf4194e8d8db1cb2d117df33f2c75c0369c3a26d96725efb978cc69e046b87e7
untagged: my-curl:latest
deleted: sha256:b2789dd875bf427de7f9f6ae001940073b3201409b14aba7e5db71f408b8569e
deleted: sha256:96daac0cb203226438989926fc34dd024f365a9a8616b93e168d303cfe4cb5e9
deleted: sha256:5cbd97a14241c9cd83250d6b6fc0649833c4a3e84099b968dd4ba403e609945e
deleted: sha256:a0971c4015c1e898c60bf95781c6730a05b5d8a2ae6827f53837e6c9d38efdec
deleted: sha256:d8359ca3b681cc5396a4e790088441673ed3ce90ebc04de388bfcd31a0716b06
deleted: sha256:83fc9ba8fb70e1da31dfcc3c88d093831dbd4be38b34af998df37e8ac538260c
deleted: sha256:ae7041a4cc625a9c8e6955452f7afe602b401f662671cea3613f08f3d9343b35
deleted: sha256:35e0f43a37755b832f0bbea91a2360b025ee351d7309dae0d9737bc96b6d0809
deleted: sha256:0af941dd29f00e4510195dd00b19671bc591e29d1495630e7e0f7c44c1e6a8c0
deleted: sha256:9fc896fc2013da84f84e45b3096053eb084417b42e6b35ea0cce5a3529705eac
deleted: sha256:47cf20d8c26c46fff71be614d9f54997edacfe8d46d51769706e5aba94b16f2b
deleted: sha256:2c675ee9ed53425e31a13e3390bf3f539bf8637000e4bcfbb85ee03ef4d910a1

Total reclaimed space: 16.43 MB
```

### Filtering(`--filter`) {#filter}

The filtering flag (`--filter`) format is of `"key=value"`. If there is more than one filter, then pass multiple flags (e.g., `--filter "foo=bar" --filter "bif=baz"`)

过滤器flag (`--filter`) 的格式是 `"key=value"`。如果有超过1个的过滤器，送入多个flag（例如：`--filter "foo=bar" --filter "bif=baz"`）

The currently supported filters are:

- until (`<timestamp>`) - only remove images created before given timestamp
- label (`label=<key>`, `label=<key>=<value>`, `label!=<key>`, or `label!=<key>=<value>`) - only remove images with (or without, in case `label!=...` is used) the specified labels.

当前支持的过滤器有：

- until (`<timestamp>`) - 仅仅删除在给定的某个时间戳之前创建的镜像
- [label](./dockerfile-LABEL.md) (`label=<key>`, `label=<key>=<value>`, `label!=<key>`, or `label!=<key>=<value>`) - 仅仅删除带有（或者不带有，在`label!=...`中）指定label的镜像。

The `until` filter can be Unix timestamps, date formatted timestamps, or Go duration strings (e.g. `10m`, `1h30m`) computed relative to the daemon machine’s time. Supported formats for date formatted time stamps include RFC3339Nano, RFC3339, `2006-01-02T15:04:05`, `2006-01-02T15:04:05.999999999`, `2006-01-02Z07:00`, and `2006-01-02`. The local timezone on the daemon will be used if you do not provide either a `Z` or a `+-00:00` timezone offset at the end of the timestamp. When providing Unix timestamps enter seconds[`.nanoseconds`], where seconds is the number of seconds that have elapsed since January 1, 1970 (midnight UTC/GMT), not counting leap seconds (aka Unix epoch or Unix time), and the optional `.nanoseconds` field is a fraction of a second no more than nine digits long.

`until`过滤器可以使用unix时间戳，日期格式的时间戳，或者用Go时间段字符串（例如：`10m`，`1h30m`）相对于宿主机器的时间进行计算。 支持的日期格式的时间戳包括RFC3339Nano, RFC3339, `2006-01-02T15:04:05`, `2006-01-02T15:04:05.999999999`, `2006-01-02Z07:00`, and `2006-01-02`。如果在时间戳的结尾没有提供`Z`或者`+-00:00`的时区偏移量，将使用宿主的时区。当提供一个Unix时间戳

---



<https://docs.docker.com/reference/cli/docker/image/prune/>
