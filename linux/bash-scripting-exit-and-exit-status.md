# Chapter 6. Exit and Exit Status

The `exit` command terminates a script, just as in a **C** program. It can also return a value, which is available to the script's parent process.

`exit`命令终止脚本，就像在C语言中一样。它可以返回一个值，这个值对于脚本的父进程是可用的。

Every command returns an exit status (sometimes referred to as a return status or exit code). A successful command returns a 0, while an unsuccessful one returns a non-zero value that usually can be interpreted as an error code. Well-behaved UNIX commands, programs, and utilities return a 0 exit code upon successful completion, though there are some exceptions.

每一个命令返回一个退出状态（有时候称为退出状态或者退出代码）。命令成功执行返回0。当不成功时返回一个非0的值，通过可以被解释为1个错误代码。良好的UNIX命令、程序、工具一旦成功完成就会返回一个0的退出代码，不过也有一些例外。

Likewise, [functions](https://tldp.org/LDP/abs/html/functions.html#FUNCTIONREF) within a script and the script itself return an exit status. The last command executed in the function or script determines the exit status. Within a script, an `exit nnn` command may be used to deliver an nnn exit status to the shell (nnn must be an integer in the 0 - 255 range).

同样的，脚本内部的[functions](https://tldp.org/LDP/abs/html/functions.html#FUNCTIONREF)和脚本自身也会返回一个退出状态。在函数或者脚本中最后执行的命令决定了退出状态。在一个脚本中，`exit nnn`命令可以被用来传递一个`nnn`的退出状态给shell（`nnn`必须是0-255之间的整数）。

When a script ends with an `exit` that has no parameter, the exit status of the script is the exit status of the last command executed in the script (previous to the exit).

当脚本由一个没有参数的`exit`结束的时候，这个脚本的退出状态是脚本中最后一行命令的退出状态（前一个退出）。

```Bash
#!/bin/bash

COMMAND_1

. . .

COMMAND_LAST

# Will exit with status of last command.
# 将以最后一个命令的status退出

exit
```

The equivalent of a bare `exit` is `exit $?` or even just omitting the `exit`.

一个裸的`exit`等价于`exit $?`，甚至可以直接省略`exit`。

```Bash
#!/bin/bash

COMMAND_1

. . .

COMMAND_LAST

# Will exit with status of last command.
# 将以最后一个命令的status退出

exit $?
```

```Bash
#!/bin/bash

COMMAND1

. . . 

COMMAND_LAST

# Will exit with status of last command.
# 将以最后一个命令的status退出
```

`$?` reads the exit status of the last command executed. After a function returns, `$?` gives the exit status of the last command executed in the function. This is Bash's way of giving functions a "return value." [^1]

`$?`读最后一条被执行的命令的退出状态。在一个方法返回后，`$?`给出的是方法中最后一个被执行的命令的退出状态。这是在Bash中，给方法一个返回值的方法。

[^1]: In those instances when there is no [return](https://tldp.org/LDP/abs/html/complexfunct.html#RETURNREF) terminating the function. 在哪些没有[return](https://tldp.org/LDP/abs/html/complexfunct.html#RETURNREF)终止function得情况下。

Following the execution of a [pipe](./bash-scripting-special-chars-pipe.md), a `$?` gives the exit status of the last command executed.

在执行管道后，`$?`给出的是最后被执行的命令的退出状态。

After a script terminates, a `$?` from the command-line gives the exit status of the script, that is, the last command executed in the script, which is, by convention, 0 on success or an integer in the range 1 - 255 on error.

在一个脚本终止后，命令行中执行`$?`会给出了scirpt退出的状态，即：脚本中最后一个被执行的命令的状态，按照惯例，成功时0，发生错误时1-255。

## Example 6-1. `exit` / `exit status`

```bash

#!/bin/bash

echo hello
echo $?    # Exit status 0 returned because command executed successfully.

lskdf      # Unrecognized command.
echo $?    # Non-zero exit status returned -- command failed to execute.

echo

exit 113   # Will return 113 to shell.
           # To verify this, type "echo $?" after script terminates.

#  By convention, an 'exit 0' indicates success,
#+ while a non-zero exit value means an error or anomalous condition.
#  See the "Exit Codes With Special Meanings" appendix.
```

```bash

#!/bin/bash

echo hello
echo $?    # 退出状态为0，因为命令被成功执行。

lskdf      # 不能识别的命令。
echo $?    # 非0的退出状态被返回，因为命令执行失败。

echo

exit 113   # 将返回123给shell
           # 为了验证，在脚本执行完后，输入`echo $?`

#  按照惯例, 'exit 0' 代表着成功,
#+ 非0的退出值，意味着错误或者异常的情况。
#  看尾目中的"Exit Codes With Special Meanings"。
```

[$?](./bash-scripting-internal-variabel-$?.md) is especially useful for testing the result of a command in a script (see [Example 16-35](https://tldp.org/LDP/abs/html/filearchiv.html#FILECOMP) and [Example 16-20](https://tldp.org/LDP/abs/html/textproc.html#LOOKUP)).

[$?](./bash-scripting-internal-variabel-$?.md)对于测试脚本中命令的结果非常有用。（看[Example 16-35](https://tldp.org/LDP/abs/html/filearchiv.html#FILECOMP) and [Example 16-20](https://tldp.org/LDP/abs/html/textproc.html#LOOKUP)）。

The !, the logical not qualifier, reverses the outcome of a test or command, and this affects its exit status.

---

<https://tldp.org/LDP/abs/html/exit-status.html>
