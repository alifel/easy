# Builtin (Bash internal)

A builtin is a command contained within the Bash tool set, literally built in. This is either for performance reasons -- builtins execute faster than external commands, which usually require forking off [^1] a separate process -- or because a particular builtin needs direct access to the shell internals.

builtin是Bash工具集内部的一个命令，按字面意思理解就是在内部实现。这要么是因为性能原因的考虑，因为内部命令的速度快过外部命令，外部命令通常要fork出来一个单独的进程，要么是因为特定的builtin需要直接访问shell内部。

A builtin may be a synonym to a system command of the same name, but Bash reimplements it internally. For example, the Bash `echo` command is not the same as /bin/echo, although their behavior is almost identical.

builtin可以与同名的系统命令synonym，但是Bash再次在内部实现了它。例如，Bash的`echo`命令和/bin/echo是不同的，尽管它们的行为几乎完全一样。

```Bash
#!/bin/bash

echo "This line uses the \"echo\" builtin."
/bin/echo "This line uses the /bin/echo system command."
```

[^1]: As Nathan Coulter points out, "while forking a process is a low-cost operation, executing a new program in the newly-forked child process adds more overhead."

---

==部分照搬==

<https://tldp.org/LDP/abs/html/internal.html#BUILTINREF>
