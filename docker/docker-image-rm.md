# docker image rm

|description|remove one or more images|
|---|---|
|**Usage**|`docker image rm [OPTIONS] IMAGE [IMAGE...]`|
|**Aliases**|`docker image remove` `docker rmi`|

|描述|删除一个或多个镜像|
|---|---|
|**用法**|`docker image rm [OPTIONS] IMAGE [IMAGE...]`|
|**别名**|`docker image remove` `docker rmi`|

## Description

Removes (and un-tags) one or more images from the host node. If an image has multiple tags, using this command with the tag as a parameter only removes the tag. If the tag is the only one for the image, both the image and the tag are removed.

移除（and un-tags）一个或者多个镜像，从本地。如果镜像有多个tag，把tag作为参数使用这个命令仅仅删除tag。如果tag仅仅对应一个镜像，则同时删除tag和镜像。

This does not remove images from a registry. You cannot remove an image of a running container unless you use the `-f` option. To see all images on a host use the `docker image ls` command.

它不会从registry中移除镜像。你不能移除一个正在运行的container的镜像，除非你使用了`-f`选项。查看本地所有的镜像使用`docker image ls` 命令。

## Options

|option|Default|Description|
|---|---|---|
|`-f --force`||Force removal of the image|
|`--no-prune`||Do not delete untagged parents|

|选项|默认值|描述|
|---|---|---|
|`-f --force`||强制删除镜像|
|`--no-prune`||不删除没有打标签的parents (:pill:==没有理解，什么作用==)|

## Examples

You can remove an image using its short or long ID, its tag, or its digest. If an image has one or more tags referencing it, you must remove all of them before the image is removed. Digest references are removed automatically when an image is removed by tag.

你可以删除一个镜像，用短或长ID，它的tag，它的digest。如果一个镜像有一个或多个tag引用它，你必须在删除这个镜像之前删除所有所有引用它的tag。digest引用被自动删除，当镜像被通过tag删除时。

```sh
$ docker images

REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
test1                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
test                      latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
test2                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)

$ docker rmi fd484f19954f

Error: Conflict, cannot delete image fd484f19954f because it is tagged in multiple repositories, use -f to force
2013/12/11 05:47:16 Error: failed to remove one or more images

$ docker rmi test1:latest

Untagged: test1:latest

$ docker rmi test2:latest

Untagged: test2:latest


$ docker images

REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
test                      latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)

 docker rmi test:latest

Untagged: test:latest
Deleted: fd484f19954f4920da7ff372b5067f5b7ddb2fd3830cecd17b96ea9e286ba5b8

```

If you use the `-f` flag and specify the image's short or long ID, then this command untags and removes all images that match the specified ID.

如果使用了`-f`，指定了镜像的短或长ID，这个命令将会untag和remove所有和这个ID匹配的镜像。

```sh
$ docker images

REPOSITORY                TAG                 IMAGE ID            CREATED             SIZE
test1                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
test                      latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)
test2                     latest              fd484f19954f        23 seconds ago      7 B (virtual 4.964 MB)

$ docker rmi -f fd484f19954f

Untagged: test1:latest
Untagged: test:latest
Untagged: test2:latest
Deleted: fd484f19954f4920da7ff372b5067f5b7ddb2fd3830cecd17b96ea9e286ba5b8
```

An image pulled by digest has no tag associated with it:

通过digest拉一个没有关联tag的镜像：

```sh
$ docker images --digests

REPOSITORY                     TAG       DIGEST                                                                    IMAGE ID        CREATED         SIZE
localhost:5000/test/busybox    <none>    sha256:cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf   4986bf8c1536    9 weeks ago     2.43 MB
```

To remove an image using its digest:

使用digest删除镜像：

```sh
$ docker rmi localhost:5000/test/busybox@sha256:cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf
Untagged: localhost:5000/test/busybox@sha256:cbbf2f9a99b47fc460d422812b6a5adff7dfee951d8fa2e4a98caa0382cfbdbf
Deleted: 4986bf8c15363d1c5d15512d5266f8777bfba4974ac56e3270e7760f6f0a8125
Deleted: ea13149945cb6b1e746bf28032f02e9b5a793523481a0a18645fc77ad53c4ea2
Deleted: df7546f9f060a2268024c8a230d8639878585defcc1bc6f79d2728a13957871b
```

---

==官方文档翻译==

<https://docs.docker.com/reference/cli/docker/image/rm/>
