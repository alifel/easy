# Builtin (Bash internal)

A builtin is a command contained within the Bash tool set, literally built in. This is either for performance reasons -- builtins execute faster than external commands, which usually require forking off [^1] a separate process -- or because a particular builtin needs direct access to the shell internals.

A builtin may be a synonym to a system command of the same name, but Bash reimplements it internally. For example, the Bash echo command is not the same as /bin/echo, although their behavior is almost identical.

```Bash
#!/bin/bash

echo "This line uses the \"echo\" builtin."
/bin/echo "This line uses the /bin/echo system command."
```

A keyword is a reserved word, token or operator. Keywords have a special meaning to the shell, and indeed are the building blocks of the shell's syntax. As examples, for, while, do, and ! are keywords. Similar to a builtin, a keyword is hard-coded into Bash, but unlike a builtin, a keyword is not in itself a command, but a subunit of a command construct. [^2]

[^1]: As Nathan Coulter points out, "while forking a process is a low-cost operation, executing a new program in the newly-forked child process adds more overhead."

[^2]: An exception to this is the [time](./bash-scripting-external-time.md) command, listed in the official Bash documentation as a keyword ("reserved word").

---

==部分照搬==

<https://tldp.org/LDP/abs/html/internal.html#BUILTINREF>
