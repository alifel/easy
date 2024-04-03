# script install

## 文档没有描述，但是遇到的特殊情况

- 当`script install ../p2`安装一个本地包后，再次安装或者卸载其他包，都会触发这`../p2`这个包的`prepare`这个生命周期script执行一次。(npm 10.5.0，node 20.12.0)
- 当``script install ../p2`安装`../p2`时，`../p2`的`prepare`不会被触发。
- 当`script install ../p2 --install-links`安装一个本地包后，紧接着下一次安装或者卸载其他包时，不会出发`../p2`的`prepare`执行，但是下一次开始，不论是安装还是卸载其他包都会触发`../p2`的`prepare`执行。(npm 10.5.0，node 20.12.0)
- 当`script install ../p2 --install-links`安装`../p2`时，`../p2`的`prepare`会被触发。
