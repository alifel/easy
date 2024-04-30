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

Optionally `COPY` accepts a flag `--from=<name>` that can be used to set the source location to a previous build stage (created with `FROM .. AS <name>`) that will be used instead of a build context sent by the user. In case a build stage with a specified name can't be found an image with the same name is attempted to be used instead.

`COPY` obeys the following rules:

- the `<src>` path is resolved relative to the build context. If you specify a relative path leading outside of the build context, such as `COPY ../something /something`, parent directory paths are stripped out automatically. The effective source path in this example becomes `COPY something /something`
- If `<src>` is a directory, the entire contents of the directory are copied, including filesystem metadata.
  :warning: ==NOTE==
  ==The directory itself isn't copied, only its contents.==
- If `<src>` is any other kind of file, it's copied individually along with its metadata. In this case, if `<dest>` ends with a trailing slash /, it will be considered a directory and the contents of `<src>` will be written at `<dest>/base(<src>)`.
- If multiple `<src>` resources are specified, either directly or due to the use of a wildcard, then `<dest>` must be a directory, and it must end with a slash `/`.
- If `<src>` is a file, and `<dest>` doesn't end with a trailing slash, the contents of `<src>` will be written as filename `<dest>`.
- If `<dest>` doesn't exist, it's created, along with all missing directories in its path.

`COPY`服从下面的规则：

- `<src>`路径的解析相对于构建context。如果你指定了相对路径导致定位到了构建context外面，例如`COPY ../something /something`，父目录路径会自动被删除。`COPY something /something`

:warning: ==NOTE==

==The first encountered COPY instruction will invalidate the cache for all following instructions from the Dockerfile if the contents of `<src>` have changed. This includes invalidating the cache for RUN instructions. See the Dockerfile Best Practices guide – Leverage build cache for more information.==

---

<https://docs.docker.com/reference/dockerfile/#copy>
