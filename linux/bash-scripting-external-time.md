# `time` (Bash external command)

Outputs verbose timing statistics for executing a command.

输出执行命令的详细统计时间

`time ls -l` / gives something like this:

```Bash
 real    0m0.067s
 user    0m0.004s
 sys     0m0.005s
```

See also the very similar [times](https://tldp.org/LDP/abs/html/x9644.html#TIMESREF) command in the previous section.

也可以在之前的章节中看到非常类似的[times](https://tldp.org/LDP/abs/html/x9644.html#TIMESREF) 命令。

As of [version 2.0](https://tldp.org/LDP/abs/html/bashver2.html#BASH2REF) of Bash, `time` became a shell reserved word, with slightly altered behavior in a pipeline.

从Bash[version 2.0](https://tldp.org/LDP/abs/html/bashver2.html#BASH2REF)开始，`time`变成了shell的保留字，在pipeline中的行为略有改变。

---

==文档照搬==

<https://tldp.org/LDP/abs/html/timedate.html#TIMREF>
