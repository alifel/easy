# Create a base image (Dcoker)

Most Dockerfiles start from a parent image. If you need to completely control the contents of your image, you might need to create a base image instead. Here's the difference:

- A [parent](./docker-parent-image.md) image is the image that your image is based on. It refers to the contents of the `FROM` directive in the Dockerfile. Each subsequent declaration in the Dockerfile modifies this parent image. Most Dockerfiles start from a parent image, rather than a base image. However, the terms are sometimes used interchangeably.
- A [base](./docker-base-image.md) image has `FROM scratch` in its Dockerfile.

大部分Dockerfile都是从一个父镜像开始的。如果你需要完全控制你镜像的内容，你需要创建一个基础镜像来代替。这是他们的不同：

- [parent](./docker-parent-image.md)镜像是你镜像的基础镜像。它Dockerfile的`FROM`指令的内容指定。Dockerfile中后续的每一个申明会修改这个镜像。大部分镜像都是从一个父镜像开始的，而不是基础镜像。然而，有时这2个术语是可以互换的。
- 一个[base](./docker-base-image.md)镜像在它的Dockerfile中有`FROM scratch`

This topic shows you several ways to create a base image. The specific process will depend heavily on the Linux distribution you want to package. We have some examples below, and you are encouraged to submit pull requests to contribute new ones.

这篇文章给你展示了一些创建基础镜像的方法。指定的进程将严重依赖于你想打包的linux发布。我们下面有一些例子，鼓励你提交pull request去贡献新的例子。

## Create a full image using tar

In general, start with a working machine that is running the distribution you'd like to package as a parent image, though that is not required for some tools like Debian's [Debootstrap](https://wiki.debian.org/Debootstrap), which you can also use to build Ubuntu images.

通常，

It can be as simple as this to create an Ubuntu parent image:

创建Ubuntu的父镜像可以非常简单，就像这样：

```bash
$ sudo debootstrap focal focal > /dev/null
$ sudo tar -C focal -c . | docker import - focal

sha256:81ec9a55a92a5618161f68ae691d092bf14d700129093158297b3d01593f4ee3

$ docker run focal cat /etc/lsb-release

DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=20.04
DISTRIB_CODENAME=focal
DISTRIB_DESCRIPTION="Ubuntu 20.04 LTS"
```