# I/O rediction

There are always three default files [^1] open, stdin (the keyboard), stdout (the screen), and stderr (error messages output to the screen). These, and any other open files, can be redirected. Redirection simply means capturing output from a file, command, program, script, or even code block within a script (see [Example 3-1](https://tldp.org/LDP/abs/html/special-chars.html#EX8) and [Example 3-2](https://tldp.org/LDP/abs/html/special-chars.html#RPMCHECK)) and sending it as input to another file, command, program, or script.

有3个默认未见一致被打开，标准输入（键盘），标准输出（屏幕），标准错误（错误消息输出到屏幕）。这些和其他任意的文件可以被重定向。重定向仅仅意味着从1个文件、命令、程序、脚本、或者脚本中的代码块获取到了输出，然后发送它作为输入到另外一个文件、命令、程序或者脚本。

Each open file gets assigned a file descriptor. [^2] The file descriptors for stdin, stdout, and stderr are 0, 1, and 2, respectively. For opening additional files, there remain descriptors 3 to 9. It is sometimes useful to assign one of these additional file descriptors to stdin, stdout, or stderr as a temporary duplicate link. [^3] This simplifies restoration to normal after complex redirection and reshuffling (see Example 20-1).

[^1]: By convention in UNIX and Linux, data streams and peripherals ([device files](https://tldp.org/LDP/abs/html/devref1.html#DEVFILEREF)) are treated as files, in a fashion analogous to ordinary files.

按UNIX和Linux的惯例，数据流和外围设备([device files](https://tldp.org/LDP/abs/html/devref1.html#DEVFILEREF))都被作为文件，类似于普通文件的处理方式。

[^2]: A file descriptor is simply a number that the operating system assigns to an open file to keep track of it. Consider it a simplified type of file pointer. It is analogous to a file handle in C.

文件描述符是操作系统赋予一个已经打开文件的简单数字，用来追踪文件。可以把它认为是简化了的文件指针类型。它与在C中处理1个文件是类似的。

[^3]: Using file descriptor 5 might cause problems. When Bash creates a child process, as with exec, the child inherits fd 5 (see Chet Ramey's archived e-mail, [SUBJECT: RE: File descriptor](http://groups.google.com/group/gnu.bash.bug/browse_thread/thread/13955daafded3b5c/18c17050087f9f37) 5 is held open). Best leave this particular fd alone.

---

<https://tldp.org/LDP/abs/html/io-redirection.html#AEN17884>
