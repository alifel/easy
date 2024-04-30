# Dockerfile WORKDIR

```Dockerfile
WORKDIR /path/to/workdir
```

The `WORKDIR` instruction sets the working directory for any `RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD` instructions that follow it in the Dockerfile. If the `WORKDIR` doesn't exist, it will be created even if it's not used in any subsequent Dockerfile instruction.

`WORKDIR`指令设置了，在这个Dockerfile中，跟在这个指令后面的`RUN`, `CMD`, `ENTRYPOINT`, `COPY` and `ADD`的工作目录。如果`WORKDIR`不存在，它将被创建，尽管它没有被用在随后的任何Dockerfile指令中。

The `WORKDIR` instruction can be used multiple times in a Dockerfile. If a relative path is provided, it will be relative to the path of the previous `WORKDIR` instruction. For example:

`WORKDIR`指令可以在同一个Dockerfile中多次使用。如果一个相对路径被提供，它将相对于之前提供的`WORKDIR`指令，例如：

```Dockerfile
WORKDIR /a
WORKDIR b
WORKDIR c
RUN pwd
```

The output of the final pwd command in this Dockerfile would be `/a/b/c`

最终在Dockerfile中用pwd命令输出`/a/b/c`

The `WORKDIR` instruction can resolve environment variables previously set using `ENV`. You can only use environment variables explicitly set in the Dockerfile. For example:

`WORKDIR`指令可以解析通过`ENV`设置的环境变量。你仅仅可以使用在Dockerfile中显示设置的环境变量。例如：

```Dockerfile
ENV DIRPATH=/path
WORKDIR $DIRPATH/$DIRNAME
RUN pwd
```

The output of the final `pwd` command in this Dockerfile would be `/path/$DIRNAME`

这个Dockerfile中的`pwd`命令最终的输出是 `/path/$DIRNAME`

If not specified, the default working directory is `/`. In practice, if you aren't building a Dockerfile from scratch (FROM scratch), the `WORKDIR` may likely be set by the base image you're using.

如果没有设置，默认的工作目录是`/`。在实践中，如果你不是从头构建一个Dockerfile（FROM scratch）,`WORKDIR` 也有可能被你使用的基础镜像设置了。

Therefore, to avoid unintended operations in unknown directories, it's best practice to set your `WORKDIR` explicitly.

因此，为了避免在未知的目录中执行以意外的操作，最佳实践是精确的设置你自己的`WORKDIR`。

--

==官方文档==

<https://docs.docker.com/reference/dockerfile/#workdir>
