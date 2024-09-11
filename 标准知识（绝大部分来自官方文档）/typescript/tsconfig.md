# tsconfig.json

## Root Dir - rootDir

Default: The longest common path of all non-declaration input files. If composite is set, the default is instead the directory containing the tsconfig.json file.

默认值：所有非声明输入文件的最长公共路径。如果设置了composite，默认值为包含tsconfig.json文件的目录。

When TypeScript compiles files, it keeps the same directory structure in the output directory as exists in the input directory.

当typescript编译文件时，它会保持与输入目录相同的目录结构。

For example, let’s say you have some input files:

```structure
MyProj
├── tsconfig.json
├── core
│   ├── a.ts
│   ├── b.ts
│   ├── sub
│   │   ├── c.ts
├── types.d.ts
```

The inferred value for rootDir is the longest common path of all non-declaration input files, which in this case is core/.

If your outDir was dist, TypeScript would write this tree:

```structure
MyProj
├── dist
│   ├── a.js
│   ├── b.js
│   ├── sub
│   │   ├── c.js
```

However, you may have intended for core to be part of the output directory structure. By setting rootDir: "." in tsconfig.json, TypeScript would write this tree:

```structure
MyProj
├── dist
│   ├── core
│   │   ├── a.js
│   │   ├── b.js
│   │   ├── sub
│   │   │   ├── c.js
```

Importantly, rootDir does not affect which files become part of the compilation. It has no interaction with the include, exclude, or files tsconfig.json settings.

Note that TypeScript will never write an output file to a directory outside of outDir, and will never skip emitting a file. For this reason, rootDir also enforces that all files which need to be emitted are underneath the rootDir path.

For example, let’s say you had this tree:

```structure
MyProj
├── tsconfig.json
├── core
│   ├── a.ts
│   ├── b.ts
├── helpers.ts
```

It would be an error to specify rootDir as core and include as * because it creates a file (helpers.ts) that would need to be emitted outside the outDir (i.e. ../helpers.js).

---

来源，官方文档：<https://www.typescriptlang.org/tsconfig/#rootDir>
