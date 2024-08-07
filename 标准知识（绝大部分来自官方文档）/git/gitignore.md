# .gitignore

## DESCRIPTION

A gitignore file specifies intentionally untracked files that Git should ignore. Files already tracked by Git are not affected; see the NOTES below for details.
一个gitignore文件指定了故意不跟踪的文件（这些文件会被Git忽略）。已经跟踪了的文件不会受到影响；看下面的细节。

Each line in a gitignore file specifies a pattern. When deciding whether to ignore a path, Git normally checks gitignore patterns from multiple sources, with the following order of precedence, from highest to lowest (within one level of precedence, the last matching pattern decides the outcome):
gitignore文件中的每一行都是一个pattern。当确认忽略的路径时，Git通常从多个地方检查gitignore的pattern，按照下面内容的顺序，优先级从最高到最低。（在同一个优先级中，最后一个匹配的pattern决定了结果）

- Patterns read from the command line for those commands that support them.

- Patterns read from a `.gitignore` file in the same directory as the path, or in any parent directory (up to the top-level of the working tree), with patterns in the higher level files being overridden by those in lower level files down to the directory containing the file. These patterns match relative to the location of the `.gitignore` file. A project normally includes such `.gitignore` files in its repository, containing patterns for files generated as part of the project build.
    从与路径相同的目录或任意父目录（一直到工作目录树的最高级）==（***:pill::pill::pill: 经过测试，工作目录树的最高级，就是`.git`目录的同级，在该级之上的`.gitignore`不产生效果***）== 的`.gitignore`中读取的patterns，目录级别越高的文件中的patterns会被目录级别较低的文件中的patterns覆盖，一直到包含文件的目录为止。这些patterns匹配的位置是相对于`.gitignore`文件的位置的。一个项目通常包括`.gitignore`这样的文件在仓库中，包含对应作为项目构建生成的文件的patterns。

- Patterns read from `$GIT_DIR/info/exclude`.
    从`$GIT_DIR/info/exclude`读取的pattern。==（***:pill::pill::pill: $GIT_DIR应该就是`.git`***）==

- Patterns read from the file specified by the configuration variable `core.excludesFile`.
    从配置变量`core.excludesFile`指定的文件中读取的patterns。

Which file to place a pattern in depends on how the pattern is meant to be used.
将pattern放在哪个文件中取决于使用该pattern的意图。

- Patterns which should be version-controlled and distributed to other repositories via clone (i.e., files that all developers will want to ignore) should go into a `.gitignore` file.
    应该进行版本控制和通过克隆分发到其他仓库的pattern（例如：所有开发者都想忽略的）应该放入`.gitignore`文件。

- Patterns which are specific to a particular repository but which do not need to be shared with other related repositories (e.g., auxiliary files that live inside the repository but are specific to one user’s workflow) should go into the `$GIT_DIR/info/exclude` file.
    特定的仓库中特有的patterns（不会被分享到其他关联的额仓库中）（例如：仓库中针对于某一个具体用户的工作流的特殊的辅助文件）应该放入`$GIT_DIR/info/exclude`文件。

- Patterns which a user wants Git to ignore in all situations (e.g., backup or temporary files generated by the user’s editor of choice) generally go into a file specified by core.excludesFile in the user’s ~/.gitconfig. Its default value is $XDG_CONFIG_HOME/git/ignore. If $XDG_CONFIG_HOME is either not set or empty, $HOME/.config/git/ignore is used instead.

The underlying Git plumbing tools, such as git ls-files and git read-tree, read gitignore patterns specified by command-line options, or from files specified by command-line options. Higher-level Git tools, such as git status and git add, use patterns from the sources specified above.

## PATTERN FORMAT

- A blank line matches no files, so it can serve as a separator for readability.
    空白行不匹配任何文件，它仅仅是作为分隔符来用的，目的是为了可阅读性。

- A line starting with `#` serves as a comment. Put a backslash ("`\`") in front of the first hash for patterns that begin with a hash.
    `#`开头的是注释。在首位的`#`前面加一个反斜杠（“`\`”），会匹配以`#`开始。

- Trailing spaces are ignored unless they are quoted with backslash ("`\`").
    后面的空格会被忽略，除非在它前面加一个反斜杠（“`\`”）。

- An optional prefix "`!`" which negates the pattern; any matching file excluded by a previous pattern will become included again. It is not possible to re-include a file if a parent directory of that file is excluded. Git doesn’t list excluded directories for performance reasons, so any patterns on contained files have no effect, no matter where they are defined. Put a backslash ("`\`") in front of the first "!" for patterns that begin with a literal "!", for example, "`!important!.txt`".
    可选的前缀“`!`”是反向匹配；之前匹配的文件会被排除，而它代表着包含。如果文件的父目录被排除了，那么这个文件时不能再次被包含的。因为考虑性能的因素，git不会列出被排除的目录，因此在被排除的文件中做任何匹配都没用，不论他们定义在哪 ==（***:pill::pill::pill: 这里一定要注意，父目录被排除了，子目录不能被再次包含，但是父目录被排除了，父目录仍然可以继续被再次包含进来。例如：pattern`/abcd/`后再加一条pattern`!/abcd/abc`是不能再次把`/abcd/abc`这个文件或者目录包含进来的，但是pattern`/abcd/`后再加一条`!/abcd/`可以把`/abcd`这个目录包含进来***）==。在首位的“!”前面加一个反斜杠（“`\`”）会匹配以“`!`”字符开头，例如：“`\!important!.txt`”。

- The slash "`/`" is used as the directory separator. Separators may occur at the beginning, middle or end of the `.gitignore` search pattern.
    斜杠“`/`”是目录分隔符。分隔符可以出现在`.gitignore` search pattern的开始、中间和结尾。

- If there is a separator at the beginning or middle (or both) of the pattern, then the pattern is relative to the directory level of the particular `.gitignore` file itself. Otherwise the pattern may also match at any level below the `.gitignore` level.
    ==如果分隔符在pattern的开始和中间，则pattern是相对于`.gitignore`自身的目录这一级的。其他情况pattern也能够匹配`.gitignore`下面的任意一级。== (:pill::warning:**这一点特别需要注意**)

- If there is a separator at the end of the pattern then the pattern will only match directories, otherwise the pattern can match both files and directories.
    ==如果分隔符在pattern的结尾，则它仅仅匹配目录，其他情况既会匹配文件又会匹配目录。== (:pill::warning:**这一点特别需要注意**)

- For example, a pattern `doc/frotz/` matches `doc/frotz` directory, but not `a/doc/frotz` directory; however `frotz/` matches `frotz` and `a/frotz` that is a directory (all paths are relative from the `.gitignore` file).
    例如，一个pattern`doc/frotz/`会匹配`doc/frotz`目录，但是不会匹配`a/doc/frotz`目录 ==（***:pill::pill::pill: Why？因为`doc/frotz/`在中间和结尾分别有一个分隔符（“`/`”），所以中间的分隔符会导致pattern相对于`.gitignore`这一级目录，所以它不会匹配`a/doc/frotz`，而结尾的分隔符会导致仅仅匹配目录，而不匹配文件***）==；可是`frotz/`匹配`frotz`和目录`a/frotz`（所有路径都相对于`.gitignore`文件） ==（***:pill::pill::pill: Why？因为`frotz/`在中间和开头都没有分隔符（“`/`”），所以这种情况会导致pattern不光相对于`.gitignore`这一级目录，也相对于`.gitignore`下面的任意一级目录，所以它会匹配`frotz`和`a/frotz`，而结尾的分隔符会导致仅仅匹配目录，而不匹配文件，所以`frotz`和`a/frotz`必须都是目录***）==。

- An asterisk "`*`" matches anything except a slash. The character "`?`" matches any one character except "`/`". The range notation, e.g. `[a-zA-Z]`, can be used to match one of the characters in a range. See fnmatch(3) and the FNM_PATHNAME flag for a more detailed description.
    一个星号“`*`”匹配除了斜杠之外任何东西。符号“`?`”匹配除了“`/`”的任意1个字符。范围标记，例如`[a-zA-z]`，可以被用来匹配这个范围内容的任意一个字符。看fnmatch(3)和FNM_PATHNAME flag了解更多细节。

Two consecutive asterisks ("`**`") in patterns matched against full pathname may have special meaning:
pattern中的2个连续不断的星号（“`**`”）

- A leading "`**`" followed by a slash means match in all directories. For example, "`**/foo`" matches file or directory "`foo`" anywhere, the same as pattern "`foo`". "`**/foo/bar`" matches file or directory "`bar`" anywhere that is directly under directory "`foo`".
    开头1个“`**`”后面跟一个斜杠意味着在所有目录中匹配。例如，“`**/foo`”匹配任意位置的“`foo`”文件和目录，和pattern“`foo`”一样。“`**/foo/bar`”匹配任意位置“`foo`”下面的“`bar`”文件或目录。

- A trailing "`/**`" matches everything inside. For example, "`abc/**`" matches all files inside directory "`abc`", relative to the location of the `.gitignore` file, with infinite depth.
    尾随其后的“`/**`”匹配里面的每一样东西。例如，“`abc/**`”匹配“`abc`”目录中的所有文件和目录（任意深度）==（***:pill::pill::pill: 注意，这里匹配的是abc目录里面的所有文件和目录，不包括abc自身***）==，“`abc`”目录相对于.igtignore的位置。

- A slash followed by two consecutive asterisks then a slash matches zero or more directories. For example, "`a/**/b`" matches "`a/b`", "`a/x/b`", "`a/x/y/b`" and so on.
    2个斜杠中间加2个连续的星号匹配0个或者多个目录，例如：“`a/**/b`”匹配“”，“`a/b`”，“`a/x/b`”，“`a/x/y/b`”等等。

- Other consecutive asterisks are considered regular asterisks and will match according to the previous rules.
    其他连续的星号被认为是普通的星号，将按之前的相关规则进行匹配。

## EXAMPLES

- The pattern `hello.*` matches any file or directory whose name begins with `hello.`. If one wants to restrict this only to the directory and not in its subdirectories, one can prepend the pattern with a slash, i.e. `/hello.*`; the pattern now matches `hello.txt`, `hello.c` but not `a/hello.java`.
    pattern`hello.*`匹配任意的名字以`hello.`开头的文件和目录。如果想限制匹配仅仅针对目录，不针对子目录，则在最前面加1个斜线，例如 `/hello.*`；pattern现在匹配`hello.txt`、`hello.c`，但是不匹配 `a/hello.java`。

- The pattern `foo/` will match a directory `foo` and paths underneath it, but will not match a regular file or a symbolic link `foo` (this is consistent with the way how pathspec works in general in Git)
    pattern `foo/` 匹配一个目录`foo`和它底下的路径，但是不会匹配一个叫`foo`的常规文件或者symbolic link（这是为了与git的路径描述符保持一致）

- The pattern `doc/frotz` and `/doc/frotz` have the same effect in any `.gitignore` file. In other words, a leading slash is not relevant if there is already a middle slash in the pattern.
    pattern `doc/frotz` 和 `/doc/frotz`在任意`.gitignore`文件中作用是一样的。换句话说，如果pattern的中间已经有了斜杠，那么为首的斜杠没有任何意义。

- The pattern `foo/*`, matches `foo/test.json` (a regular file), `foo/bar` (a directory), but it does not match `foo/bar/hello.c` (a regular file), as the asterisk in the pattern does not match `bar/hello.c` which has a slash in it.
    pattern `foo/*` 匹配 `foo/test.json` (一个常规文件), `foo/bar` (一个目录), 但是不匹配 `foo/bar/hello.c` (一个常规文件), 因为星号不匹配 `bar/hello.c` （它有一个斜杠在里面）。

```shell
    $ git status
    [...]
    # Untracked files:
    [...]
    #       Documentation/foo.html
    #       Documentation/gitignore.html
    #       file.o
    #       lib.a
    #       src/internal.o
    [...]
    $ cat .git/info/exclude
    # ignore objects and archives, anywhere in the tree.
    *.[oa]
    $ cat Documentation/.gitignore
    # ignore generated html files,
    *.html
    # except foo.html which is maintained by hand
    !foo.html
    $ git status
    [...]
    # Untracked files:
    [...]
    #       Documentation/foo.html
    [...]
```

Another example:

```shell
    $ cat .gitignore
    vmlinux*
    $ ls arch/foo/kernel/vm*
    arch/foo/kernel/vmlinux.lds.S
    $ echo '!/vmlinux*' >arch/foo/kernel/.gitignore
```

The second `.gitignore` prevents Git from ignoring `arch/foo/kernel/vmlinux.lds.S`.
第2个`.gitignore`阻止git忽略`arch/foo/kernel/vmlinux.lds.S`。

Example to exclude everything except a specific directory `foo/bar` (note the `/*` - without the slash, the wildcard would also exclude everything within `foo/bar`):
下面的例子排除了除`foo/bar`以外的everything（注意：如果`/*`没有斜杠后，通配符会匹配下文中`foo/bar`中的everything进行排除）：

```shell
    $ cat .gitignore
    # exclude everything except directory foo/bar
    /*
    !/foo
    /foo/*
    !/foo/bar
```

==（***:pill::pill::pill: 上方代码解析：`/*`首先排除了与`.gitignore`同级的所有文件和目录；然后`!/foo`又包含进来了与`.gitignore`同级的`foo`目录或文件；然后`/foo/*`又排除掉了`/foo`目录下的所有目录和文件；然后`!/foo/bar`又包含进来了`/foo`目录下的`bar`文件或者目录***）==

---

==以上是文档的部分内容截取和翻译==

<https://git-scm.com/docs/gitignore>

git version 2.44.0

整理时间：2024年3月30日
