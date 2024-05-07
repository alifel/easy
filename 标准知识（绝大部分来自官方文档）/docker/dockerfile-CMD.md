# Dockerfile CMD

The `CMD` instruction sets the command to be executed when running a container from an image.

`CMD`指令设置通过镜像运行一个容器时被执行的命令。

You can specify `CMD` instructions using [shell or exec forms](./dockerfile-Shell-and-exec-form.md):

你可以用[shell or exec forms](./dockerfile-Shell-and-exec-form.md)指定`CMD`指令：

- `CMD ["executable","param1","param2"]` (exec form)
- `CMD ["param1","param2"]` (exec form, as default parameters to ENTRYPOINT)
- `CMD command param1 param2` (shell form)

There can only be one `CMD` instruction in a Dockerfile. If you list more than one `CMD`, only the last one takes effect.

在一个Dockerfile中，只能有一个`CMD`指令。如果你列出了多个`CMD`，仅仅最后一个有效。

The purpose of a `CMD` is to provide defaults for an executing container. These defaults can include an executable, or they can omit the executable, in which case you must specify an [`ENTRYPOINT`](./dockerfile-ENTRYPOINT.md) instruction as well.

`CMD`的目的是为一个执行的容器提供一个默认值。这些默认值可以包含1个可执行文件，或者在你制定了`ENTRYPOINT`指令的情况下，可以省略可执行文件。

If `CMD` is used to provide default arguments for the `ENTRYPOINT` instruction, both the `CMD` and `ENTRYPOINT` instructions should be specified in the exec form.

如果`CMD`指令被用来提供`ENTRYPOINT`指令的参数，则`CMD`和`ENTRYPOINT`指令都应该使用exec形式。

:warning: ==NOTE==

==Don't confuse `RUN` with `CMD`. `RUN` actually runs a command and commits the result; `CMD` doesn't execute anything at build time, but specifies the intended command for the image.==

==不要混淆`RUN`和`CMD`，`RUN`实际执行一个命令并提交结果；`CMD`不执行任何命令在构建时，但是指定目标命令给镜像。==

---

==官方文档==

<https://docs.docker.com/reference/dockerfile/#cmd>
