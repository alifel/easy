# docker parent image

An image's parent image is the image designated in the `FROM` directive in the image's Dockerfile. All subsequent commands are based on this parent image. A Dockerfile with the `FROM scratch` directive uses no parent image, and creates a base image.

一个父镜像是在Dockerfile中用`FROM`直接指定的镜像。所有后面的命令都基于这个镜像。一个使用`FROM scratch`的Dockerfile不使用父镜像，创建一个基础镜像。

---

==官方文档==

<https://docs.docker.com/glossary/#parent-image>
