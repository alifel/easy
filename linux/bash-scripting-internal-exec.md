# `exec` (Bash internal builtin)

This shell builtin replaces the current process with a specified command. Normally, when the shell encounters a command, it forks off a child process to actually execute the command. Using the `exec` builtin, the shell does not fork, and the command `exec`'ed replaces the shell. When used in a script, therefore, it forces an exit from the script when the `exec`'ed command terminates. [^7]

这个shell builtin替换了用指定的命令替换了当前的进程。通常，当shell遇到一个命令时，它会fork一个子进程来实际执行这个命令。使用`exec` builtin，shell不会fork，被`exec`的命令替换shell。当在脚本中使用时，当被`exec`的命令终止时，他会强制从脚本退出。[^7]

Example 15-24. Effects of `exec`

```bash
#!/bin/bash

exec echo "Exiting \"$0\" at line $LINENO."   # Exit from script here.
# $LINENO is an internal Bash variable set to the line number it's on.

# ----------------------------------
# The following lines never execute.

echo "This echo fails to echo."

exit 99                       #  This script will not exit here.
                              #  Check exit value after script terminates
                              #+ with an 'echo $?'.
                              #  It will *not* be 99.
```

```bash
#!/bin/bash

exec echo "Exiting \"$0\" at line $LINENO."   # 在这儿退出script.
# $LINENO 是一个Bash内部的变量， 在它上面设置了行号.

# ----------------------------------
# 下面的代码不会被执行.

echo "This echo fails to echo."

exit 99                       #  这个script不会在这退出.
                              #  在script终止后，用'echo $?'查看exit值，
                              #
                              #  它不是99
```

Example 15-25. A script that exec's itself

```bash
#!/bin/bash
# self-exec.sh

# Note: Set permissions on this script to 555 or 755,
#       then call it with ./self-exec.sh or sh ./self-exec.sh.

echo

echo "This line appears ONCE in the script, yet it keeps echoing."
echo "The PID of this instance of the script is still $$."
#     Demonstrates that a subshell is not forked off.

echo "==================== Hit Ctl-C to exit ===================="

sleep 1

exec $0   #  Spawns another instance of this same script
          #+ that replaces the previous one.

echo "This line will never echo!"  # Why not?

exit 99                            # Will not exit here!
                                   # Exit code will not be 99!
```

```bash
#!/bin/bash
# self-exec.sh

# Note: 设置脚本的权限，从555设置为755,
#       然后通过 ./self-exec.sh 或者 sh ./self-exec.sh调用它

echo

echo "This line appears ONCE in the script, yet it keeps echoing."
echo "The PID of this instance of the script is still $$."
#     Demonstrates that a subshell is not forked off.

echo "==================== Hit Ctl-C to exit ===================="

sleep 1

exec $0   #  Spawns another instance of this same script
          #+ that replaces the previous one.

echo "This line will never echo!"  # Why not?

exit 99                            # Will not exit here!
                                   # Exit code will not be 99!
```

[^7]: Unless the exec is used to [reassign file descriptors](https://tldp.org/LDP/abs/html/x17974.html#USINGEXECREF).

An `exec` also serves to reassign file descriptors. For example, `exec <zzz-file` replaces stdin with the file zzz-file.

The `-exec` option to find is not the same as the `exec` shell builtin.

---

<https://tldp.org/LDP/abs/html/internal.html#EXECREF>
