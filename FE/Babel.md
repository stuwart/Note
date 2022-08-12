## Babel

### 概述

`Babel` 是一个专门将高版本的`JavaScript` 转化为较低的版本，使得低版本的浏览器或者环境能够兼容。比如将ES6专为ES5.

#### 主要功能

+ 转换语法
+ `Polyfill` 目标环境缺少的功能
+ 源代码转换

```javascript
ES6:
[1,2,3].map(n => n*2);

Babel转为ES5:
[1,2,3].map(function(n){
  return n*2;
});
```





### 使用方法

1.安装对应的`Babel` 插件

` npm install @babel/core @babel/cli @babel/preset-env`

2.使用以下内容在项目的根目录中创建`babel.config.json`配置文件 （新版本用JSON）

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        },
        "useBuiltIns": "usage",
        "corejs": "3.6.5"
      }
    ]
  ]
}
```

3.运行命令，将代码从`src` 编译到`lib`

```shell
./node_modules/.bin/babel src --out-dir lib
```



### Babel CLI

Babel的**核心内容**基于**核心模块** `@babel/core` 

#### CLI工具

##### 安装使用

```shell
npm install --save-dev @babel/core @babel/cli

./node_modules/.bin/babel src --out-dir lib
```

说明：它使用预设的规则解析了`src`的JS文件，并将转换后的文件输出到`lib` 目录。解析规则需要预先指定并作为参数传给CLI。



#### plugins

​	**规则** 以插件的形式体现。如箭头函数转换插件等等……

#### presets

​	如果插件无法完全满足需求，那么可以使用一个`preset` ，其包含着一组预设好的插件，这样就不用我们手动地一个个添加需要用到的插件了。

​	`preset` 像是一套工具包，像Java中的`Maven` 的`starter` 和`parent`。

**使用**

```shell
npm install --save-dev @babel/preset-env

./node_modules/.bin/babel src --out-dir lib --presets=@babel/env
//该preset包含了支持ES6的所有插件。
```



#### 配置文件

`babel.config.json`

```json
{
  "presets": [
    [
      "@babel/preset-env",
      {
        "targets": {
          "edge": "17",
          "firefox": "60",
          "chrome": "67",
          "safari": "11.1"
        },
         "useBuiltIns": "usage"//使用这段代码，可以让Babel自动查找缺少的功能，并只包含对应的那个polyfill，不过多导入
      }
    ]
  ]
}
```

#### 小结

我们使用 `@babel/cli` 从终端运行 Babel，`@babel/polyfill` 用于 polyfill 所有新的 JavaScript 功能，`env`preset 只包含我们使用的功能的转换规则，polyfills 用于填充目标浏览器中缺少的功能。

> 以上内容摘自[Babel中文官方文档](https://babel.docschina.org/docs/en/usage/)



### 配置

#### babel.config.json （推荐）

场景：使用单体式仓库或者想要编译`node_modules`

```json
{
  "presets": [...],
  "plugins": [...]
}
```

​	使用单体式仓库时，在根目录创建该文件，但是还需要告诉`Babel` 在哪里查找对应配置（即root），因此可在`Webpack` 中进行对应配置。

```javascript
module: {
  rules: [
    {
      loader: "babel-loader",
      options: {
        rootMode: "upward",
      },
    },
  ];
}
```





#### .babelrc.json

场景：仅适用于项目的单个部分



### 预设

#### 官方预设

+ [@babel/preset-env](https://babel.docschina.org/docs/en/babel-preset-env) 用于编译 ES2015+ 语法
+ [@babel/preset-typescript](https://babel.docschina.org/docs/en/babel-preset-typescript) 用于 [TypeScript](https://www.typescriptlang.org/)
+ [@babel/preset-react](https://babel.docschina.org/docs/en/babel-preset-react) 用于 [React](https://reactjs.org/)
+ [@babel/preset-flow](https://babel.docschina.org/docs/en/babel-preset-flow) 用于 [Flow](https://flow.org/)

使用方法：直接将预设名写在presets配置选项内即可。

:warning:注意：预设加载是倒着来的。写在后面的先加载运行。





### 插件

+ 转换类插件（启用转换类插件后自动开启语法类插件）
+ 语法类插件

> [常用插件列表](https://babel.docschina.org/docs/en/plugins-list/)
