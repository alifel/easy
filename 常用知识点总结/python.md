# python

## 使用虚拟环境

```shell
python3 -m venv myenv
source myenv/bin/activate
pip install torch 
```

### 我来详细解释这三个命令：

`python3 -m venv myenv`
这个命令创建一个新的虚拟环境。
python3 调用 Python 3 解释器。
-m venv 告诉 Python 运行 venv 模块。venv 是 Python 3.3+ 版本内置的虚拟环境创建工具。
myenv 是你要创建的虚拟环境的名称。这将创建一个名为 myenv 的新目录。
这个命令会在当前目录下创建一个 myenv 文件夹，其中包含一个独立的 Python 解释器、标准库和支持文件。

`source myenv/bin/activate`
这个命令激活刚才创建的虚拟环境。
source 是 bash 的内置命令，用于执行文件中的命令。
myenv/bin/activate 是虚拟环境的激活脚本。
执行这个命令后，你的命令提示符可能会改变，显示当前激活的虚拟环境名称（如 (myenv) $）。此时，python 和 pip 命令会使用虚拟环境中的版本，而不是系统全局的版本。

`pip install torch`
这个命令在激活的虚拟环境中安装 PyTorch。
pip 是 Python 的包管理工具。在虚拟环境中，它指向环境专属的 pip 版本。
install 是 pip 的子命令，用于安装包。
torch 是 PyTorch 包的名称。
这个命令会下载并安装 PyTorch 及其所有依赖，但只安装在当前激活的虚拟环境中，不会影响系统的其他 Python 环境。

**使用虚拟环境的好处：**
隔离：每个项目可以有自己的依赖，不会相互干扰。
版本控制：可以在不同环境中使用不同版本的包。
清洁：不会污染全局 Python 环境。
可复制：易于在其他机器上重现相同的环境。
当你完成工作后，可以使用 deactivate 命令退出虚拟环境。如果你想删除虚拟环境，只需删除 myenv 文件夹即可。
