---
title: Using React+Typescript+Antd To Build
date: "2021-04-29T17:11:57.284Z"
description: 一直没有将Typescript正式的运用到项目中，而且现在React以及Antd对Typescript的支持都很完善，所以这次搭建一个支持Typescript的基础项目环境。
---

# React+Typescript+antd 搭建项目

## 初始化项目

开发环境:
> node: v14.15.0<br>
> yarn: 1.22.10<br>
> npx: 7.8.0<br>
> editor: visual studio code<br>

使用`cra`进行项目初始化
```shell
npx create-react-app my-app --template typescript
```

如果已经使用`cra`创建过项目, 执行:
```shell
npm install --save typescript @types/node @types/react @types/react-dom @types/jest

# or

yarn add typescript @types/node @types/react @types/react-dom @types/jest
```
官方文档关于这一节的[介绍](https://create-react-app.dev/docs/adding-typescript/)

## antd

### 安装依赖
antd 本身已支持`typescript4+`, 不要使用`@types/antd`, [详见](https://ant.design/docs/react/use-in-typescript-cn)
```shell
yarn add antd
```

### 按需加载

可以使用暴露webpack配置的方式进行, 但是`yarn eject`这种方式是不可逆的, 可能会人为造成不必要的损失, 官方也不推荐

这里采用`create-react-app`官方给出的[方法](https://github.com/timarney/react-app-rewired)

引入`react-app-rewired`、`customize-cra`和`babel-plugin-import`

babel-plugin-import 是一个按需加载代码或样式的插件, create-app-rewired 需要配和 customize-cra 使用

```shell
yarn add react-app-rewired customize-cra babel-plugin-import
```

修改`package.json`

```javascript
"scripts": {
-   "start": "react-scripts start",
-   "build": "react-scripts build",
-   "test": "react-scripts test",

+   "build": "react-app-rewired build",
+   "start": "react-app-rewired start",
+   "test": "react-app-rewired test",
	"eject": "react-scripts eject"
}
```

在`根目录`下创建`config-overrides.js`

```javascript
+ const { override, fixBabelImports } = require+ ("customize-cra");
+ 
  // antd 使用 import 按需打包(babel-plugin-import)
+ module.exports = override(
+   fixBabelImports("import", {
+     libraryName: "antd",
+     libraryDirectory: "es",
+     style: "css"
+   }),
+ );
```

`yarn start` 重启, 配置加载完成

### 自定义主题

通过配置对less中的主题进行`覆写`, 使用 `customize-cra` 中的`addLessLoader`

安装依赖:
```shell
yarn add less less-loader
```

在`config-overrides.js` 中添加配置
```javascript
const { override, fixBabelImports, addLessLoader } = require("customize-cra");

module.exports = override(
  fixBabelImports("import", {
    libraryName: "antd",
    libraryDirectory: "es",
-    style: "css",
+    style: true
  }),
+  addLessLoader({
+    javascriptEnabled: true,
+    modifyVars: {
+      "@primary-color": "#8ace57"
+    }
+  })
);
```
使用`lessloader`的`modifyVars` 进行主题配置, [其他变量和配置方式](https://ant.design/docs/react/customize-theme-cn)

`yarn star` 重启项目, 主题更改成功
> 这一步可能会出现报错, `TypeError: this.getOptions is not a function` <br>

原因: less-loader版本过高<br>
解决办法: 
```shell
yarn remove less-loader
yarn add less-loader@5
```

`PS:` [All React+Typescript速查表](https://github.com/typescript-cheatsheets/react#reacttypescript-cheatsheets)
