# Dockerfile COPY

`COPY` has two forms. The latter form is required for paths containing whitespace.

`COPY`有2个格式。后一种格式适用于包含空格的路径。

```Dockerfile
COPY [OPTIONS] <src> ... <dest>
COPY [OPTIONS] ["<src>", ... "<dest>"]
```

The available `[OPTIONS]` are:

可用的`[OPTIONS]`有：

- `-- from`
- `-- chown`
- `-- chmod`
- `-- link`
- `-- parents`
- `-- exclude`

The `COPY` instruction copies new files or directories from `<src>` and adds them to the filesystem of the container at the path `<dest>`.

`COPY`指令从`<src>`复制新文件或者目录，添加它们到容器文件系统的`<dest>`路径下。

Multiple `<src>` resources may be specified but the paths of files and directories will be interpreted as relative to the source of the context of the build.

也可以指定多个`<src>`，但是文件和目录的路径解释时，是相对于构建context的。（:pill:比如`/`代表的是构建context的根目录，而不是本地主机的根目录）(:pill:==重中之重==)

Each `<src>` may contain wildcards and matching will be done using Go's [filepath.Match](./dockerignore.md#filepath-match-function) rules. For example:

每个`<src>`也可以包含通配符，将使用Go的[filepath.Match](./dockerignore.md#filepath-match-function) 规则进行匹配。例如：

To add all files in the root of the build context starting with "hom":

添加构建上下文根目录中以 "hom" 开头的所有文件：

```Dockerfile
COPY hom* /mydir/
```

In the following example, `?` is a single-character wildcard, matching e.g. `"home.txt"`.

在下面的例子中，`?`是一个单字符同撇符，匹配例如`"home.txt"`。

```Dockerfile
COPY hom?.txt /mydir/
```

The `<dest>` is an absolute path, or a path relative to `WORKDIR`, into which the source will be copied inside the destination container.

`<dest>`是一个绝对路径，或者`WORKDIR`的相对路径，将源文件复制到目标容器中。(:pill:==重中之重==)

The example below uses a relative path, and adds `"test.txt"` to `<WORKDIR>/relativeDir/`:

下面的例子使用了相对路径，把`"test.txt"` 加入到了 `<WORKDIR>/relativeDir/`：

```Dockerfile
COPY test.txt relativeDir/
```

Whereas this example uses an absolute path, and adds `"test.txt"` to `/absoluteDir/`

然而这个例子使用了绝对路径，增加 `"test.txt"` 去 `/absoluteDir/`

```Dockerfile
COPY test.txt /absoluteDir/
```

When copying files or directories that contain special characters (such as `[` and `]`), you need to escape those paths following the Golang rules to prevent them from being treated as a matching pattern. For example, to copy a file named `arr[0].txt`, use the following;

当复制文件或者目录包含字符（如：`[` and `]`）时，你需要按Golang的规则转义这些路径，以阻止他们被视为匹配pattern。例如，你可以复制1个名叫`arr[0].txt`的文件，用下面的内容：

```Dockerfile
COPY arr[[]0].txt /mydir/
```

==:warning: NOTE==

==If you build using STDIN (`docker build - < somefile`), there is no build context, so COPY can't be used.==

==如果你构建时使用了STDIN (`docker build - < somefile`), 这会导致没有构建上context，所以COPY不能使用。==

Optionally `COPY` accepts a flag --from=`<name>` that can be used to set the source location to a previous build stage (created with FROM .. AS `<name>`) that will be used instead of a build context sent by the user. In case a build stage with a specified name can't be found an image with the same name is attempted to be used instead.

---

<https://docs.docker.com/reference/dockerfile/#copy>
