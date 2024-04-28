# .dockerignore

You can use a `.dockerignore` file to exclude files or directories from the build context.

你可以用一个`.dockerignore`文件排除文件和目录，从构建上下文中。

```.dockerignore
# .dockerignore
node_modules
bar
```

This helps avoid sending unwanted files and directories to the builder, improving build speed, especially when using a remote builder.

这帮助你避免不必要的文件和目录参与构建，提高构建速度，当使用远程构建时效果更明显。

## Filename and location

When you run a build command, the build client looks for a file named `.dockerignore` in the root directory of the context. If this file exists, the files and directories that match patterns in the files are removed from the build context before it's sent to the builder.

当你运行一个构建命令，构建客户端寻找一个名叫`.dockerignore`的文件，在上下文的根目录中 ==（:pill:这个特别注意，是上下文的根目录，搞清楚什么叫上下文）== 。如果这个文件存在，根文件中规则匹配的文件和目录在发送给构造器之前从构建上下文中被移除。

If you use multiple Dockerfiles, you can use different ignore-files for each Dockerfile. You do so using a special naming convention for the ignore-files. Place your ignore-file in the same directory as the Dockerfile, and prefix the ignore-file with the name of the Dockerfile, as shown in the following example.

如果你使用了多Dockerfile文件，你可以为每一个Dockerfile使用不同的ignore-file。要这样做，你要对ignore-file使用一个特定的命名规范。把你的ignore-file放到和Dockerfile同级的目录下，ignore-file的前缀使用Dockerfile的名称，如下面例子。

```text
.
├── index.ts
├── src/
├── docker
│   ├── build.Dockerfile
│   ├── build.Dockerfile.dockerignore
│   ├── lint.Dockerfile
│   ├── lint.Dockerfile.dockerignore
│   ├── test.Dockerfile
│   └── test.Dockerfile.dockerignore
├── package.json
└── package-lock.json
```

A Dockerfile-specific ignore-file takes precedence over the `.dockerignore` file at the root of the build context if both exist.

Dockerfile指定的ignore-file优先于构建上下文根目录下的`.dockerignore`文件，如果他们都存在的话。

## Syntax

The `.dockerignore` file is a newline-separated list of patterns similar to the file globs of Unix shells. For the purposes of matching, the root of the context is considered to be both the working and the root directory. For example, the patterns `/foo/bar` and `foo/bar` both exclude a file or directory named `bar` in the `foo` subdirectory of `PATH` or in the root of the Git repository located at URL. Neither excludes anything else.

`.dockerignore`文件是一个用新行做分隔的匹配规则列表，类似于unix的file globs。为了匹配的目的，上下文的根目录被认为是工作目录和根目录。例如，`/foo/bar`和`foo/bar`都会排除在`PATH`或者URL对应的Git仓库的根目录下的`foo`目录下，1个名叫`bar`文件或者目录。不会排除其他任何东西。

If a line in `.dockerignore` file starts with `#` in column 1, then this line is considered as a comment and is ignored before interpreted by the CLI.

如果`.dockerignore`文件里的一行以`#`开头，那么这个行会被认为是注释，在CLI解释之前会被忽略。

If you're interested in learning the precise details of the `.dockerignore` pattern matching logic, check out the [moby/patternmatcher repository](https://github.com/moby/patternmatcher/tree/main/ignorefile) on GitHub, which contains the source code.

如果你有兴趣学习`.dockerignore`规则匹配的精确细节，可以在GitHub检出[moby/patternmatcher repository](https://github.com/moby/patternmatcher/tree/main/ignorefile) ，它包含了源码。

## Matching

The following code snippet shows an example `.dockerignore` file.

下面的代码片段展示了一个`.dockerignore`文件。

```text
# comment
*/temp*
*/*/temp*
temp?
```

This file causes the following build behavior:

这个文件会有下面的构建行为：

|Rule|Behavior|
|---|---|
|`# comment`|Ignored.|
|`# comment`|被忽略。|
|`*/temp*`|Exclude files and directories whose names start with `temp` in any immediate subdirectory of the root. For example, the plain file `/somedir/temporary.txt` is excluded, as is the directory `/somedir/temp`.|
|`*/temp*`|排除以`temp`作为名字开头的文件和目录（它们在根目录的任意下一级目录中）。例如，`/somedir/temporary.txt`会被排除，目录`/somedir/temp`一样会被排除。|
|`*/*/temp*`|Exclude files and directories starting with `temp` from any subdirectory that is two levels below the root. For example, `/somedir/subdir/temporary.txt` is excluded.|
|`*/*/temp*`|排除以`temp`作为名字开头的文件和目录（他们在根目录下任意二级子目录下），例如，`/somedir/subdir/temporary.txt`会被排除。|
|`temp?`|Exclude files and directories in the root directory whose names are a one-character extension of `temp`. For example, `/tempa` and `/tempb` are excluded.|
|`temp?`|排除根目录下名字为`temp`后+任意字母的文件和目录，例如，`/tempa` 和 `/tempb` 会被排除。|

Matching is done using Go's [filepath.Match function](#filepath-match-function) rules. A preprocessing step uses Go's [filepath.Clean function](#filepath-clean-function) to trim whitespace and remove `.` and `..`. Lines that are blank after preprocessing are ignored.

匹配使用了Go的[filepath.Match function](#filepath-match-function) 方法规则。预处理步骤使用了GO的[filepath.Clean function](#filepath-clean-function)来去掉开头结尾的空格和移除`.`,`..`。在预处理之后空白的行被忽略。

:warning: ==**NOTE**: For historical reasons, the pattern `.` is ignored.==

:warning: ==**注意**: 由于历史原因，`.`规则被忽略。==

Beyond Go's `filepath.Match` rules, Docker also supports a special wildcard string `**` that matches any number of directories (including zero). For example, `**/*.go` excludes all files that end with `.go` found anywhere in the build context.

超出Go的`filepath.Math`规则，Docker也支持`**`通配符，来匹配任意数量的目录（包括0级）。例如，`**/*.go`将会排除所有以`.go`结尾的文件(:pill:==我个人觉得还应该包括目录==)（在构建上下文的任意位置）

You can use the `.dockerignore` file to exclude the `Dockerfile` and `.dockerignore` files. These files are still sent to the builder as they're needed for running the build. But you can't copy the files into the image using `ADD`, `COPY`, or bind mounts.

你也可以使用`.dockerignore`文件排除`Dockerfile`和`.dockerignore`文件。这些文件仍然会发送给构建器，因为它们在构建的时候需要。但是你不能复制这些文件取你的镜像（通过`ADD`, `COPY`, 或者 bind mounts）。

---

## 引用

### `filepath.Match` function {#filepath-match-function}

```go
func Match(pattern, name string) (matched bool, err error)
```

Match reports whether name matches the shell file name pattern. The pattern syntax is:

`Match`报告名字和规则文件名是否匹配。下面是规则语法：

```text
pattern:
	{ term }
term:
	'*'         matches any sequence of non-Separator characters
	'?'         matches any single non-Separator character
	'[' [ '^' ] { character-range } ']'
	            character class (must be non-empty)
	c           matches character c (c != '*', '?', '\\', '[')
	'\\' c      matches character c

character-range:
	c           matches character c (c != '\\', '-', ']')
	'\\' c      matches character c
	lo '-' hi   matches character c for lo <= c <= hi
```

```text
pattern:
	{ term }
term:
	'*'         匹配没有分隔符的任意一串字符
	'?'         匹配任意单个非分隔符字符
	'[' [ '^' ] { character-range } ']'
	            character class (must be non-empty)
	c           匹配任意字符c (c不能是后面任意字符，'*'、 '?'、 '\\'、 '[')
	'\\' c      匹配任意字符c（如果不懂，看下面的例子）

character-range:
	c           匹配任意字符c (c不能是后面任意字符，'\\'、 '-'、 ']')
	'\\' c      匹配任意字符c
	lo '-' hi   匹配字符c， 它大于等于lo字符，小于等于hi字符
```

Match requires pattern to match all of name, not just a substring. The only possible returned error is `ErrBadPattern`, when pattern is malformed.

`Match`要求规则匹配名字的所有，而不进进是一个子串。当规则有缺陷的时候，会返回一个`ErrBadPattern`的错误。

On Windows, escaping is disabled. Instead, '\\' is treated as path separator.

在Windows中，escaping是禁用的。`\\`被当作路径分隔符。

#### Example

```Go
package main

import (
	"fmt"
	"path/filepath"
)

func main() {
	fmt.Println("On Unix:")
	fmt.Println(filepath.Match("/home/catch/*", "/home/catch/foo"))
	fmt.Println(filepath.Match("/home/catch/*", "/home/catch/foo/bar"))
	fmt.Println(filepath.Match("/home/?opher", "/home/gopher"))
	fmt.Println(filepath.Match("/home/\\*", "/home/*"))

}
```

out:

```text
On Unix:
true <nil>
false <nil>
true <nil>
true <nil>
```

来源：<https://pkg.go.dev/path/filepath#Match>

### `filepath.Clean` Function {#filepath-clean-function}

```Go
func Clean(path string) string
```

`Clean` returns the shortest path name equivalent to path by purely lexical processing. It applies the following rules iteratively until no further processing can be done:

`Clean`通过纯文本处理返回最短有效路径。它利用下面的规则迭代，知道没有能处理的为止：

1. Replace multiple Separator elements with a single one.
2. Eliminate each `.` path name element (the current directory).
3. Eliminate each inner `..` path name element (the parent directory) along with the non-.. element that precedes it.
4. Eliminate `..` elements that begin a rooted path: that is, replace "/.." by "/" at the beginning of a path, assuming Separator is '/'.

翻译：

1. 用1个分隔符替换多个分隔符。
2. 移除每一个`.`路径名元素（代表当前目录的）。
3. 移除每一个内部的`..`路径名元素（父母路），以及紧挨着它之前的非`..`目录。
4. 移除跟路径开始的`..`，即用`"/"`替换`"/.."`，假设分隔符是`'/'`。

The returned path ends in a slash only if it represents a root directory, such as `"/"` on Unix or `C:\` on Windows.

被返回的路径仅仅在它代表根目录时才以斜杠结尾。例如 Unix 上的 "/" 或 Windows 上的 C:\。

Finally, any occurrences of slash are replaced by Separator.

最终任意斜杠都会被分隔符替换。

If the result of this process is an empty string, Clean returns the string ".".

如果经过这个处理后，结果是一个空字符串，Clean会返回字符串"."。

On Windows, Clean does not modify the volume name other than to replace occurrences of "/" with `\`. For example, Clean("//host/share/../x") returns `\\host\share\x`.

在Windows上，Clean不会修改卷名，除了将斜杠替换为`\`。例如，Clean("//host/share/../x") 返回 `\\host\share\x`。

See also Rob Pike, “Lexical File Names in Plan 9 or Getting Dot-Dot Right,” <https://9p.io/sys/doc/lexnames.html>

来源：<https://pkg.go.dev/path/filepath#Clean>

---

==官方文档==

<https://docs.docker.com/build/building/context/#dockerignore-files>
