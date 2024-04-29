# fock (Bash)

When a command or the shell itself initiates (or spawns) a new subprocess to carry out a task, this is called forking. This new process is the child, and the process that forked it off is the parent. While the child process is doing its work, the parent process is still executing.

当一个命令或者shell自己初始化（or spawns）1个新的子进程去处理一个任务时，被叫作forking。这个新进程是子进程，fork它出来的是父进程。当子进程完成了它的工作，它的父进程仍然运行。

Note that while a parent process gets the process ID of the child process, and can thus pass arguments to it, the reverse is not true. [This can create problems that are subtle and hard to track down](https://tldp.org/LDP/abs/html/gotchas.html#PARCHILDPROBREF).

注意，虽然父进程获可以获取子进程的进程ID，因此可以传递参数给它，但是反过来则不成立。[This can create problems that are subtle and hard to track down](https://tldp.org/LDP/abs/html/gotchas.html#PARCHILDPROBREF)。

## Example 15-1. A script that spawns multiple instances of itself

```bash
#!/bin/bash
# spawn.sh


PIDS=$(pidof sh $0)  # Process IDs of the various instances of this script.
P_array=( $PIDS )    # Put them in an array (why?).
echo $PIDS           # Show process IDs of parent and child processes.
let "instances = ${#P_array[*]} - 1"  # Count elements, less 1.
                                      # Why subtract 1?
echo "$instances instance(s) of this script running."
echo "[Hit Ctl-C to exit.]"; echo


sleep 1              # Wait.
sh $0                # Play it again, Sam.

exit 0               # Not necessary; script will never get to here.
                     # Why not?

#  After exiting with a Ctl-C,
#+ do all the spawned instances of the script die?
#  If so, why?

# Note:
# ----
# Be careful not to run this script too long.
# It will eventually eat up too many system resources.

#  Is having a script spawn multiple instances of itself
#+ an advisable scripting technique.
#  Why or why not?
```

Generally, a Bash builtin does not fork a subprocess when it executes within a script. An external system command or filter in a script usually will fork a subprocess.

通常，在脚本执行时，bash的[builtin](./bash-scripting-internal-builtin.md)不会创建子进程。脚本中的外部的命令或者过滤器会创建1个子进程。

---

==文档照搬==

<https://tldp.org/LDP/abs/html/internal.html#FORKREF>
