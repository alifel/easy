# Dockerfile LABEL

```Dockerfile
LABEL <key>=<value> <key>=<value> <key>=<value> ...
```

The `LABEL` instruction adds metadata to an image. A `LABEL` is a key-value pair. To include spaces within a `LABEL` value, use quotes and backslashes as you would in command-line parsing. A few usage examples:

`LABEL`指令增加元数据到一个镜像。`LABEL`是一个健值对。为了在`LABEL`值中包括空格，使用引号或者反斜杠在你的命令行中。下面是使用的例子：

```Dockerfile
LABEL "com.example.vendor"="ACME Incorporated"
LABEL com.example.label-with-value="foo"
LABEL version="1.0"
LABEL description="This text illustrates \
that label-values can span multiple lines."
```

An image can have more than one label. You can specify multiple labels on a single line. Prior to Docker 1.10, this decreased the size of the final image, but this is no longer the case. You may still choose to specify multiple labels in a single instruction, in one of the following two ways:

一个镜像可以有超过1个label。你可以指定多个Label在同一行。在Docker1.10之前，这会减少最终镜像的大小，但是现在不再是这样。你可以仍然选择指定多个labels在1个指令中，用下面2个方法中的1个：

```Dockerfile
LABEL multi.label1="value1" multi.label2="value2" other="value3"
```

```Dockerfile
LABEL multi.label1="value1" \
      multi.label2="value2" \
      other="value3"
```

==:warning: Note==

==Be sure to use double quotes and not single quotes. Particularly when you are using string interpolation (e.g. `LABEL example="foo-$ENV_VAR"`), single quotes will take the string as is without unpacking the variable's value.==

==请确保使用双引号而不是单引号。特别当你使用字符串的插值变量时 (e.g. `LABEL example="foo-$ENV_VAR"`，`$ENV_VAR`就是插值变量), 单引号不会解析字符串中差值变量的值==

Labels included in base or parent images (images in the `FROM` line) are inherited by your image. If a label already exists but with a different value, the most-recently-applied value overrides any previously-set value.

包含在基础或者父镜像（`FROM` 行的镜像）中的label会被你的镜像继承。如果一个label已经存在了，但是有不同的值，那么最新应用的值会覆盖之前的值。

To view an image's labels, use the `docker image inspect` command. You can use the `--format` option to show just the labels;

为了浏览镜像的label，可以使用`docker image inspect`命令。你可使用`--format`option来仅仅展示label；

```sh
$ docker image inspect --format='{{json .Config.Labels}}' myimage
```

```json
{
  "com.example.vendor": "ACME Incorporated",
  "com.example.label-with-value": "foo",
  "version": "1.0",
  "description": "This text illustrates that label-values can span multiple lines.",
  "multi.label1": "value1",
  "multi.label2": "value2",
  "other": "value3"
}
```

---

==官方文档翻译==

<https://docs.docker.com/reference/dockerfile/#label>
