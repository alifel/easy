# Shell and exec form (Dockerfile)

The `RUN`, `CMD`, and `ENTRYPOINT` instructions all have two possible forms:

`RUN`、`CMD`和`ENTRYPOINT`指令一共有2种形式：

- `INSTRUCTION ["executable","param1","param2"]` (exec form)
- `INSTRUCTION command param1 param2` (shell form)

The exec form makes it possible to avoid shell string munging, and to invoke commands using a specific command shell, or any other executable. It uses a JSON array syntax, where each element in the array is a command, flag, or argument.

exec form可以避免shell字符串处理，使用指定的command shell(:pill:==不会自动调用shell==)或者其他的可执行文件调用命令，或者其他可执行的。它使用JSON的数组语法，数组中的每一个元素是一个命令、flag或者参数。

The shell form is more relaxed, and emphasizes ease of use, flexibility, and readability. The shell form automatically uses a command shell, whereas the exec form does not.

shell form更加宽松，强调使用简单，灵活性和可读性。shell form会自动使用命令shell，而exec form不会。

## Exec form

The exec form is parsed as a JSON array, which means that you must use double-quotes (") around words, not single-quotes (').

exec form会被当作JSON数组来分析，意味着你必须用双引号来包裹命令，而不是单引号。

```Dockerfile
ENTRYPOINT ["/bin/bash", "-c", "echo hello"]
```

The exec form is best used to specify an `ENTRYPOINT` instruction, combined with `CMD` for setting default arguments that can be overridden at runtime. For more information, see [ENTRYPOINT](./dockerfile-ENTRYPOINT.md).

exec form最适合用于`ENTRYPOINT`指令，结果`CMD`用于设置在运行时可以被覆盖的默认参数。更多信息，看[ENTRYPOINT](./dockerfile-ENTRYPOINT.md)。

### Variable substitution

Using the exec form doesn't automatically invoke a command shell. This means that normal shell processing, such as variable substitution, doesn't happen. For example, RUN `[ "echo", "$HOME" ]` won't handle variable substitution for `$HOME`.

使用exec form不会自动调用shell。这意味着正常的shell处理，例如变量替换，不会发生。例如`[ "echo", "$HOME" ]`不会处理`$HOME`的变量替换。

If you want shell processing then either use the shell form or execute a shell directly with the exec form, for example: `RUN [ "sh", "-c", "echo $HOME" ]`. When using the exec form and executing a shell directly, as in the case for the shell form, it's the shell that's doing the environment variable substitution, not the builder.

如果你想shell处理，要么使用shell form，或者直接用exec form执行shell，例如，`RUN [ "sh", "-c", "echo $HOME" ]`。当使用exec form，直接执行shell，就像在shell form一样，它的shell会做环境变量的替换，这里注意，不是builder做环境变量替换。

### Backslashes

In exec form, you must escape backslashes. This is particularly relevant on Windows where the backslash is the path separator. The following line would otherwise be treated as shell form due to not being valid JSON, and fail in an unexpected way:

在exec form中，你必须转义反斜杠。在Windows中这是非常有意义的，反斜杠在Windows中时路径分隔符。The following line would otherwise be treated as shell form due to not being valid JSON, and fail in an unexpected way:

```Dockerfile
RUN ["c:\windows\system32\tasklist.exe"]
```

The correct syntax for this example is:

正确的语法例子是：

```Dockerfile
RUN ["c:\\windows\\system32\\tasklist.exe"]
```

## Shell form

Unlike the exec form, instructions using the shell form always use a command shell. The shell form doesn't use the JSON array format, instead it's a regular string. The shell form string lets you escape newlines using the escape character (backslash by default) to continue a single instruction onto the next line. This makes it easier to use with longer commands, because it lets you split them up into multiple lines. For example, consider these two lines:

不像exec form，指令使用shell form，会一直使用command shell。shell form不会使用JSON数组形式，而是常规字符串。shell form字符串让你使用[escape characer](https://docs.docker.com/reference/dockerfile/#escape)（默认是反斜杠）来连接新行。这使它更容易使用长命令，因为它让你把命令放入多行。例如，考虑下面这2行：

```Dockerfile
RUN source $HOME/.bashrc && \
echo $HOME
```

You can also use heredocs with the shell form to break up a command:

你也可以用在shellform的heredocs中断行：

```Dockerfile
RUN <<EOF
source $HOME/.bashrc && \
echo $HOME
EOF
```

For more information about heredocs, see [Here-documents](../linux/bash-scripting-here-document.md).

更多关于heredocs的信息，看[Here-documents](../linux/bash-scripting-here-document.md)。

---

==官方文档==

<https://docs.docker.com/reference/dockerfile/#shell-and-exec-form>
