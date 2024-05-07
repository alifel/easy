# `exit` (Bash internal)

Unconditionally terminates a script. [^6] The `exit` command may optionally take an integer argument, which is returned to the shell as the exit status of the script. It is good practice to end all but the simplest scripts with an exit 0, indicating a successful run.

无条件的终止script。`exit`也可以选择一个数字参数，返回给shell作为script退出的状态。除了最简单的script，用`exit 0`标示全部结束（成功运行），是一个非常好的实践。

If a script terminates with an `exit` lacking an argument, the exit status of the script is the exit status of the last command executed in the script, not counting the `exit`. This is equivalent to an exit `$?`.

如果脚本因没有参数的`exit`终止，脚本的退出状态是脚本中最后一个命令执行的退出状态，不包含`exit`命令本身。这相当于一个`exit $?`。

An `exit` command may also be used to terminate a [subshell](https://tldp.org/LDP/abs/html/subshells.html#SUBSHELLSREF)

`exit`命令也可以用来终止 [subshell](https://tldp.org/LDP/abs/html/subshells.html#SUBSHELLSREF)。

[^6]: Technically, an `exit` only terminates the process (or shell) in which it is running, not the parent process. 技术上，仅仅终止执行它的进程（或者shell），而不是父进程。

---

==文档照搬==

<https://tldp.org/LDP/abs/html/internal.html#EXITREF>
