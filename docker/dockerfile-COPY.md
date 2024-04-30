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

(:pill:==上面这句不知道如何理解==)

Each `<src>` may contain wildcards and matching will be done using Go's [filepath.Match](./dockerignore.md#filepath-match-function) rules. For example:

每个`<src>`也可以包含通配符，将使用Go的[filepath.Match](./dockerignore.md#filepath-match-function) 规则进行匹配。例如：

To add all files in the root of the build context starting with "hom":

添加构建上下文根目录中以 "hom" 开头的所有文件：

```Dockerfile
COPY hom* /mydir/
```

---

<https://docs.docker.com/reference/dockerfile/#copy>
