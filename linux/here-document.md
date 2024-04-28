# Chapter 19. Here Documents

A *here document* is a special-purpose code block. It uses a form of [I/O redirection](https://tldp.org/LDP/abs/html/io-redirection.html#IOREDIRREF) to feed a command list to an interactive program or a command, such as ftp, cat, or the ex text editor.

一个*here document*是一个有特殊用途的代码块。它使用了一种[I/O redirection](https://tldp.org/LDP/abs/html/io-redirection.html#IOREDIRREF)格式来向一个命令列出交互式的称呼或者命令，例如ftp，cat或ex文本编辑器。

```sh
COMMAND <<InputComesFromHERE
...
...
...
InputComesFromHERE
```

A *limit string* delineates (frames) the command list. The special symbol `<<` precedes the limit string. This has the effect of redirecting the output of a command block into the stdin of the program or command. It is similar to interactive-program < command-file, where command-file contains

一个*limit string*标示命令列表。特殊符号`<<`在限制的字符串之前。它有一个重定向的作用，一个命令块的输出进入了程序或者命令的标准输入。它类似`interactive-program < command-file`中，command-file包含的对象。

```sh
command #1
command #2
...
```

The *here document* equivalent looks like this:

等价的here document如下：

```sh
interactive-program <<LimitString
command #1
command #2
...
LimitString
```

Choose a *limit string* sufficiently unusual that it will not occur anywhere in the command list and confuse matters.

选择一个相当独特的*limit string*，它不能存在于命令列表的任何地方，也不能发生混淆。

Note that *here documents* may sometimes be used to good effect with non-interactive utilities and commands, such as, for example, `wall`.

注意，*here documents*有时候可以被用来和非交互式工具和命令一起有效的使用，例如，`wall`。

## Example 19-1. broadcast: Sends message to everyone logged in

```shell
#!/bin/bash

wall <<zzz23EndOfMessagezzz23
E-mail your noontime orders for pizza to the system administrator.
    (Add an extra dollar for anchovy or mushroom topping.)
# Additional message text goes here.
# Note: 'wall' prints comment lines.
zzz23EndOfMessagezzz23

# Could have been done more efficiently by
#         wall <message-file
#  However, embedding the message template in a script
#+ is a quick-and-dirty one-off solution.

exit
```

Even such unlikely candidates as the vi text editor lend themselves to *here documents*.

即使像vi这样的编辑器也可以给他们自己提供*here document*，虽然这个候选者不太可能。

---

==以上是官方文档的部分节选，省略掉了其他例子==

<https://tldp.org/LDP/abs/html/here-docs.html>
