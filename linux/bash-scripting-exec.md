# exec

This shell builtin replaces the current process with a specified command. Normally, when the shell encounters a command, it forks off a child process to actually execute the command. Using the exec builtin, the shell does not fork, and the command exec'ed replaces the shell. When used in a script, therefore, it forces an exit from the script when the exec'ed command terminates. [7]

这个shell会用当前指定的命令替换当前的进程。通常，当shell遇到一个命令，他会

---

<https://tldp.org/LDP/abs/html/internal.html#EXECREF>
