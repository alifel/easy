# Build Context

The `docker build` and `docker buildx build` commands build Docker images from a [Dockerfile](https://docs.docker.com/reference/dockerfile/) and a context.

`docker build`和`docker buildx build`命令，从一个[Dockerfile](https://docs.docker.com/reference/dockerfile/)和一个context构建Docker镜像。

## What is a build context?

The build context is the set of files that your build can access. The positional argument that you pass to the build command specifies the context that you want to use for the build:

构建context时一组文件，你构建时可以访问的。你送入构建命令的位置参数制定了你用来构建使用的context：

```text
$ docker build [OPTIONS] PATH | URL | -
                         ^^^^^^^^^^^^^^
```

You can pass any of the following inputs as the context for a build:

- The relative or absolute path to a local directory
- A remote URL of a Git repository, tarball, or plain-text file
- A plain-text file or tarball piped to the `docker build` command through standard input

你送入的任意在列输入都会作为构建context：

- 一个本地目录的相对或者绝对路径
- 一个远程git仓库或者tarball或者普通文本文件的URL
- 一个普通文本文件或者tarball通过标准输入送入`docker build`命令

### Filesystem contexts

When your build context is a local directory, a remote Git repository, or a tar file, then that becomes the set of files that the builder can access during the build. Build instructions such as `COPY` and `ADD` can refer to any of the files and directories in the context.

当你的构建context是一个本地目录，远程Git仓库，或者一个tar文件时，然他它们会变为一组文件提供给构建器在构建的时候访问。构建命令如`COPY`、`ADD`可以引用任意文件或者目录（在context中的）。

A filesystem build context is processed recursively:

- When you specify a local directory or a tarball, all subdirectories are included
- When you specify a remote Git repository, the repository and all submodules are included

一个文件系统构建context是被递归执行的：

- 当你指定了一个本地的目录或者tartball，它们的所有的子目录都被包括
- 当你指定了一个远程git仓库，仓库和所有子模块都会被包括

For more information about the different types of filesystem contexts that you can use with your builds, see:

- [Local files](#local-context)
- [Git repositories](#git-repositories)
- [Remote tarballs](#remote-tarballs)

关于不同文件系统的context（你构建时用的）的更多信息，看:

- [Local files](#local-context)
- [Git repositories](#git-repositories)
- [Remote tarballs](#remote-tarballs)

### Text file contexts

When your build context is a plain-text file, the builder interprets the file as a `Dockerfile`. With this approach, the build doesn't use a filesystem context.

当你的构建context是一个普通文件，构建器会把这个文件当作`Dockerfile`来解析。对于这种情况，构建不使用文件系统context。

For more information, see [empty build context](#empty-context).

更多信息看[empty build context](#empty-context)。

## Local Context {#local-context}

To use a local build context, you can specify a relative or absolute filepath to the `docker build` command. The following example shows a build command that uses the current directory (`.`) as a build context:

为了使用本地构建context，你可以指定1个相对或者文件路径给`docker build`命令。下面的例子展示了1个构建命令使用当前的目录(`.`)作为构建context:

```text
$ docker build .
...
#16 [internal] load build context
#16 sha256:23ca2f94460dcbaf5b3c3edbaaa933281a4e0ea3d92fe295193e4df44dc68f85
#16 transferring context: 13.16MB 2.2s done
...
```

This makes files and directories in the current working directory available to the builder. The builder loads the files it needs from the build context when needed.

这让构造器可以使用当前工作目录中的文件和目录。构造器加载（当需要时）文件需要从构造context中。

You can also use local tarballs as build context, by piping the tarball contents to the docker build command. See [Tarballs](#local-tarballs).

你可以使用本地tarballs作为构建上下文，通过将tarball内容传递给docker build命令。看[Tarballs](#local-tarballs)。

### Local directories

Consider the following directory structure:

考虑下面的目录结构：

```text
.
├── index.ts
├── src/
├── Dockerfile
├── package.json
└── package-lock.json
```

`Dockerfile` instructions can reference and include these files in the build if you pass this directory as a context.

在构建中，如果送入目录作为context，`Dockerfile` 的命令可以引用和包含送入目录的这些文件。

```Dockerfile
# syntax=docker/dockerfile:1
FROM node:latest
WORKDIR /src
COPY package.json package-lock.json .
RUN npm ci
COPY index.ts src .
```

```text
$ docker build .
```

### Local context with Dockerfile from stdin

Use the following syntax to build an image using files on your local filesystem, while using a `Dockerfile` from stdin.

当从标准输入中使用`Dockerfile`时，使用下面的语法使用你本地文件系统的文件构建一个镜像。

```text
$ docker build -f- PATH
```

The syntax uses the `-f` (or `--file`) option to specify the `Dockerfile` to use, and it uses a hyphen (`·`) as filename to instruct Docker to read the `Dockerfile` from stdin.

这个语法使用了`-f`（或者`--file`）选项指定了被使用的`Dockerfile`，它用一个连字符(`-`)作为文件名，来告诉Docker通过标准输入读取`Dockerfile`

The following example uses the current directory (`.`) as the build context, and builds an image using a `Dockerfile` passed through stdin using a here-document.

下面的例子使用了当前目录（`.`）作为了构建context，使用了通过标准输入（[使用here-document](../linux/here-document.md)）送入的`Dockerfile`构建了一个对象。

```sh
# create a directory to work in
mkdir example
cd example

# create an example file
touch somefile.txt

# build an image using the current directory as context
# and a Dockerfile passed through stdin
docker build -t myimage:latest -f- . <<EOF
FROM busybox
COPY somefile.txt ./
RUN cat /somefile.txt
EOF
```

==(:pill: 上面用了`EOF`作为 ***here document***的 ***limit string***，关于 ***limit string***，请看[使用here-document](../linux/here-document.md)。 其实这里`EOF`可以替换成任意字符串，只要足够特别，在要输入的代码块中不存在，不会造成混淆就可以)==

==看下面这个例子，与上面的代码作用相同（自己写的，非官方文档）==

```sh
# create a directory to work in
mkdir example
cd example

# create an example file
touch somefile.txt

# build an image using the current directory as context
# and a Dockerfile passed through stdin
docker build -t myimage:latest -f- . <<jn123nn
FROM busybox
COPY somefile.txt ./
RUN cat /somefile.txt
jn123nn
```

### Local tarballs {#local-tarballs}

When you pipe a tarball to the build command, the build uses the contents of the tarball as a filesystem context.

当你通过管道给build命令送入一个tarball，会使用这个tarball的内容作为一个filesystem context进行构建。

For example, given the following project directory:

例如，给下面的项目目录：

```text
├── Dockerfile
├── Makefile
├── README.md
├── main.c
├── scripts
├── src
└── test.Dockerfile
```

You can create a tarball of the directory and pipe it to the build for use as a context:

你可以创建一个这个目录的tarball，然后通过管道送入构建器来作为一个context：

```sh
 $ tar czf foo.tar.gz *
 $ docker build - < foo.tar.gz
```

The build resolves the `Dockerfile` from the tarball context. You can use the `--file` flag to specify the name and location of the `Dockerfile` relative to the root of the tarball. The following command builds using `test.Dockerfile` in the tarball:

构建器从tarball context中解析`Dockerfile`。你也可以使用`--file` flag去指定`Dockerfile`的名字和位置（相对于tarball的根目录）。下面的命令，构建时使用了tarball中的 `text.Dockerfile`。

```sh
$ docker build --file test.Dockerfile - < foo.tar.gz
```

## Remote Context

You can specify the address of a remote Git repository, tarball, or plain-text file as your build context.

- For Git repositories, the builder automatically clones the repository. See Git repositories.
- For tarballs, the builder downloads and extracts the contents of the tarball. See Tarballs.

你指定一个远程仓库地址

If the remote tarball is a text file, the builder receives no filesystem context, and instead assumes that the remote file is a Dockerfile. See Empty build context.

### Git repositories {#git-repositories}

### Remote tarballs {#remote-tarballs}

## Empty context {#empty-context}

---

<https://docs.docker.com/build/building/context/#what-is-a-build-context>
