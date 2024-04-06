# What is the exact difference between a 'terminal', a 'shell', a 'tty' and a 'console'?

In Unix terminology, the short answer is that

- terminal = tty = text input/output environment
- console = physical terminal
- shell = command line interpreter

---

Console, terminal and tty are closely related. Originally, they meant a piece of equipment through which you could interact with a computer: In the early days of Unix, that meant a [teleprinter](https://en.wikipedia.org/wiki/Teleprinter)-style device resembling a typewriter, sometimes called a teletypewriter, or “tty” in shorthand. The name “terminal” came from the electronic point of view, and the name “console” from the furniture point of view. Very early in Unix history, electronic keyboards and displays became the norm for terminals.

console、terminal和tty时密切相关的。最初，它们代表着一种设备（通过这些设备你可以与计算机交互）：在unix早起，它们指一个像打字机一样的[teleprinter](https://en.wikipedia.org/wiki/Teleprinter)类型的设备，有时候被叫作电传打印机或者使用“tty”作为缩写。“terminal”来源于电子角度，“console”来源于装置角度。在unix历史的早期，电子键盘和显示器变成了terminal的标配。

In Unix terminology, a **tty** is a particular kind of [device file](https://en.wikipedia.org/wiki/Device_file) which implements a number of additional commands ([ioctls](https://en.wikipedia.org/wiki/Ioctl#Terminals)) beyond read and write. In its most common meaning, **terminal** is synonymous with tty. Some ttys are provided by the kernel on behalf of a hardware device, for example with the input coming from the keyboard and the output going to a text mode screen, or with the input and output transmitted over a serial line. Other ttys, sometimes called pseudo-ttys, are provided (through a thin kernel layer) by programs called **[terminal emulators](https://en.wikipedia.org/wiki/Terminal_emulator)**, such as [Xterm](https://en.wikipedia.org/wiki/Xterm) (running in the [X Window System](https://en.wikipedia.org/wiki/X_Window_System)), [Screen](https://en.wikipedia.org/wiki/GNU_Screen) (which provides a layer of isolation between a program and another terminal), [SSH](https://en.wikipedia.org/wiki/Secure_Shell) (which connects a terminal on one machine with programs on another machine), [Expect](https://en.wikipedia.org/wiki/Expect) (for scripting terminal interactions), etc.

在unix术语中，**tty** 是一种特定的[device file](https://en.wikipedia.org/wiki/Device_file) ，指定了一些除了读和写以外的命令（[ioctls](https://en.wikipedia.org/wiki/Ioctl#Terminals)）。比较通用的理解是，**terminal** 与 **tty** 是同义词。有些tty是被内核作为硬件的代表提供的，例如从键盘来的输入和输出到文字模式的屏幕，或者同时包含输入和输出传播的串行线。其他的ttys有时候也被叫作伪tty，它被（通过1个分离的内核层）程序提供，被叫作**[terminal emulators](https://en.wikipedia.org/wiki/Terminal_emulator)**，例如： [Xterm](https://en.wikipedia.org/wiki/Xterm) (运行在 [X Window System](https://en.wikipedia.org/wiki/X_Window_System)), [Screen](https://en.wikipedia.org/wiki/GNU_Screen) (在程序和其他terminal中间提供了一层隔离层), [SSH](https://en.wikipedia.org/wiki/Secure_Shell) (用另一个机器上的程序链接这台机器上的terminal), [Expect](https://en.wikipedia.org/wiki/Expect) (脚本交互), 等等。

The word terminal can also have a more traditional meaning of a device through which one interacts with a computer, typically with a keyboard and a display. For example an X terminal is a kind of [thin client](https://en.wikipedia.org/wiki/Thin_client), a special-purpose computer whose only purpose is to drive a keyboard, display, mouse and occasionally other human interaction peripherals, with the actual applications running on another, more powerful computer.

单词terminal还有一个更传统的意义，一个可以通过它与计算机交互的设备，通常有键盘和显示器。例如，X terminal是一种微客户端，它是一种特殊的计算机，它的目的仅仅是驱动键盘、显示器、鼠标和其他与人类交互的外围设备，实际应用程序运行在其他强大的计算机上。

A **console** is generally a terminal in the physical sense that is by some definition the primary terminal directly connected to a machine. The console appears to the operating system as a (kernel-implemented) tty. On some systems, such as Linux and FreeBSD, the console appears as several ttys (special key combinations switch between these ttys); just to confuse matters, the name given to each particular tty can be “console”, ”virtual console”, ”virtual terminal”, and other variations.

**console** 通常是一个terminal（在物理上直接连接到机器上的主要terminal）。console出现在操作系统中，作为一个（被内核实现的）tty。在一些系统中，例如linux和freeBSD，console会有数个tty（特殊的组合件可以在这些tty中间进行切换）；为了避免混淆，给每一个特定的tty一个名字，它们可以是“console”、“vertual console”、“virtual terminal”，和其他。

[See also Why is a Virtual Terminal “virtual”, and what/why/where is the “real” Terminal?.](https://askubuntu.com/q/14284/1059?_gl=1*txurvd*_ga*MTMzOTQ1NjI3LjE3MTIzNjk4MzI.*_ga_S812YQPLT2*MTcxMjM3MjQwMi4yLjAuMTcxMjM3MjQwMi4wLjAuMA..)

---

A **[shell](https://en.wikipedia.org/wiki/Shell_%28computing%29)** is the primary interface that users see when they log in, whose primary purpose is to start other programs. (I don't know whether the original metaphor is that the shell is the home environment for the user, or that the shell is what other programs are running in.)

**[shell](https://en.wikipedia.org/wiki/Shell_%28computing%29)** 时用户登陆时的主要界面，它的主要目的是启动其他程序。（我不知道是最初的意义是，它代表（home环境），还是代表着其他程序运行在它里面。）

In Unix circles, **shell** has specialized to mean a [command-line shell](https://en.wikipedia.org/wiki/Shell_%28computing%29#Command-line_shells), centered around entering the name of the application one wants to start, followed by the names of files or other objects that the application should act on, and pressing the Enter key. Other types of environments don't use the word “shell”; for example, window systems involve “[window managers](https://en.wikipedia.org/wiki/Window_manager)” and “[desktop environments](https://en.wikipedia.org/wiki/Desktop_environment)”, not a “shell”.

在unix领域中，**shell** 已经专门指代[command-line shell](https://en.wikipedia.org/wiki/Shell_%28computing%29#Command-line_shells)，主要围绕输入你想启动的应用程序名字，（后面跟一个文件或者其他应用程序要使用的对象的名字），然后键入回车键。其他类型的环境不使用单词“shell”；例如，窗口操作系统涉及的“[window managers](https://en.wikipedia.org/wiki/Window_manager)” 和 “[desktop environments](https://en.wikipedia.org/wiki/Desktop_environment)”就不是“shell”。

There are many different Unix shells. Popular shells for interactive use include [Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) (the default on most Linux installations), [Zsh](https://en.wikipedia.org/wiki/Z_shell) (which emphasizes power and customizability) and [fish](https://en.wikipedia.org/wiki/Fish_(Unix_shell)) (which emphasizes simplicity).

有很多不同的unix shell。比较流行的shell包括[Bash](https://en.wikipedia.org/wiki/Bash_(Unix_shell)) (大部分linux默认安装), [Zsh](https://en.wikipedia.org/wiki/Z_shell)  (强调能力和可定制性) and [fish](https://en.wikipedia.org/wiki/Fish_(Unix_shell)) (强调简单)。

Command-line shells include flow control constructs to combine commands. In addition to typing commands at an interactive prompt, users can write scripts. The most common shells have a common syntax based on the [Bourne shell](https://en.wikipedia.org/wiki/Bourne_shell). When discussing “shell programming”, the shell is almost always implied to be a Bourne-style shell. Some shells that are often used for scripting but lack advanced interactive features include the [KornShel (ksh)](https://en.wikipedia.org/wiki/KornShell) and many [ash](https://en.wikipedia.org/wiki/Almquist_shell) variants. Pretty much any Unix-like system has a Bourne-style shell installed as `/bin/sh`, usually ash, ksh or Bash.

命令行shell包括流程控制结构去组合命令。除了在交互的提示中键入命令，用户可以写脚本。大部分通用的shell都有通用的语法（基于 [Bourne shell](https://en.wikipedia.org/wiki/Bourne_shell)）。当讨论shell编程时，shell几乎暗指Bourne-style shell。一些经常被用来编写脚本的shell，缺乏高级的交互功能，包括 [KornShel (ksh)](https://en.wikipedia.org/wiki/KornShell)和许多[ash](https://en.wikipedia.org/wiki/Almquist_shell)的变种。几乎所有的类unix系统都有一个Bourne-style的shell被安装，作为`/bin/sh`，通常是ash、 ksh 或者 Bash。

In Unix system administration, a user's **shell** is the program that is invoked when they log in. Normal user accounts have a command-line shell, but users with restricted access may have a [restricted shell](https://en.wikipedia.org/wiki/Restricted_shell) or some other specific command (e.g. for file-transfer-only accounts).

在unix系统管理中，用户的 **shell** 是用户登陆时调用的程序。普通账号有一个命令行shell，但是具有受限访问的账号可能只有一个[restricted shell](https://en.wikipedia.org/wiki/Restricted_shell)或其他特定的命令（例如，仅仅传输文件的账号）。

---

The division of labor between the terminal and the shell is not completely obvious. Here are their main tasks:

terminal和shell的工作区别不是特别明显。下面列出了它们主要的任务：

- Input: the terminal converts keys into control sequences (e.g. Left → \e[D). The shell converts control sequences into commands (e.g. \e[D → backward-char).
    输入过程中：terminal转换按键为控制序列（例如：左箭头 → \e[D）。shell转换控制序列到命令 (例如： \e[D → backward-char)。
- Line editing, input history and completion are provided by the shell.
    行编辑，输入历史，自动完成由shell提供。
  - The terminal may provide its own line editing, history and completion instead, and only send a line to the shell when it's ready to be executed. The only common terminal that operates in this way is M-x shell in Emacs.
    terminal也可以提供自己的行编辑，输入历史，和自动完成，只有当它准备好执行一行时，才会发送给shell。唯一常见采用这种方法的terminal时Emacs的M-x。
- Output: the shell emits instructions such as “display foo”, “switch the foreground color to green”, “move the cursor to the next line”, etc. The terminal acts on these instructions.
    输出过程：shell产生指令，例如“display foo”, “switch the foreground color to green”, “move the cursor to the next line”等等。terminal的行为依赖于这些指令。
- The prompt is purely a shell concept.
    提示时shell的概念。
- The shell never sees the output of the commands it runs (unless redirected). Output history (scrollback) is purely a terminal concept.
    shell看到不命令的输出（除非重定向）。输出历史是terminal的概念。
- Inter-application copy-paste is provided by the terminal (usually with the mouse or key sequences such as Ctrl+Shift+V or Shift+Insert). The shell may have its own internal copy-paste mechanism as well (e.g. Meta+W and Ctrl+Y).
    内部程序，复制粘贴是被terminal提供的（通常是按键序列，例如：Ctrl+Shift+V 或 Shift+Insert）。shell也有它内部的复制粘贴组件（例如：Meta+W 和 Ctrl+Y）。
- [Job control](https://en.wikipedia.org/wiki/Job_control_(Unix)) (launching programs in the background and managing them) is mostly performed by the shell. However, it's the terminal that handles key combinations like Ctrl+C to kill the foreground job and Ctrl+Z to suspend it.
    [Job control](https://en.wikipedia.org/wiki/Job_control_(Unix))（在后台启动程序并管理它们），几乎所有都被shell执行。但是terminal它处理组合键Ctrl+C杀死前台工作的工作，使用Ctrl+Z中止它。

原文：<https://unix.stackexchange.com/questions/4126/what-is-the-exact-difference-between-a-terminal-a-shell-a-tty-and-a-con>
