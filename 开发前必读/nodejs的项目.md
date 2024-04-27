# nodejs的项目

## 去掉`package.json`中的`main`，并且增加`"private:true"`，防止被发布到npm

**内容出处：<https://webpack.js.org/guides/getting-started>**

> We also need to adjust our `package.json` file in order to make sure we mark our package as `private`, as well as removing the `main` entry. This is to prevent an accidental publish of your code.
