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

The `WORKDIR` instruction can resolve environment variables previously set using ENV. You can only use environment variables explicitly set in the Dockerfile. For example:
