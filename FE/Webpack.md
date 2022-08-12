## Webpack

### 概述

**Webpack** 是最流行的打包工具之一，是前端资源构建工具。它会将所有的资源文件作为一个个模块进行处理，并根据这些模块的依赖关系进行分析，最终打包，生成对应的静态资源。

### 核心概念

#### 入口 - entry

**入口起点** 告诉 `Webpack` 应该使用哪个模块来生成对应的依赖关系，进入**入口起点** 后，`Webpack` 会找出其依赖的模块。

##### 默认值

`./src/index.js` 

##### 配置

可以通过在 `webpack.configuration` 中配置，从而指定一个或多个入口起点

```javascript
//webpack.config.js

单个入口：（简写）
module.exports = {
  entry: './path/to/my/entry/file.js',
}

多个入口：
modules.exports = {
  entry: ['./src/xxx.js', './src/xxx.js']
}

对象语法：
module.exports = {
  entry: {
    app: './src/app.jx',
    adminApp:'./src/adminApp.js',
  },
};
```

##### 属性

用于描述入口的对象。

+ `dependOn`：当前入口所依赖的入口，加载当前入口前必须先加载依赖入口
+ `filename`：指定要输出的文件名称
+ `import`：启动时需要加载的模块
+ `library`：为当前入口构建一个library
+ `runtime`：运行时chunk的名字，如果设置了就会创建一个新的运行时chunk，可设置为`false`来避免一个新的运行时chunk
+ `publicPath`：当该入口的输出文件在浏览器中被引用时，制定一个公共的URL





#### 输出 - output

**输出** 告诉 `webpack` 应该在哪里输出其创建的 bundle，以及如何命名这些文件。

##### 默认值

主要输出文件：`./dist/main.js` 

其他生成文件：`./dist`

##### 配置

```javascript
// webpack.config.js

const path = require('path');
module.exports = {
  output : {
    path: path.resolve(__dirname,'dist'),
    filename: 'xxx.bundle.js',
    //对于多个入口，可用占位符 [name]
    //filename: '[name].js'
  }
}
```



#### Loader

`webpack` 默认只能理解 `JavaScript` 与 `JSON` 文件，其下载后自动支持，要让其能够理解其它类型的文件，并转化为对应的模块，需要使用**loader**

##### 属性

+ `test` ：识别出哪些文件会被转换
+ `use`  ：定义出在进行转换时使用哪个 **loader**

##### 配置

+ config.js

```javascript
// webpack.config.js

module.exports = {
  module: {
    rules: [
      {  test:/\.txt$/, 
         use:'raw-loader'
      },
      {
         test:/\.css$/,
         use:'css-loader'
      },
    ],
  }
}
```

该配置告诉`webpack` 在处理 txt文件时，先用`raw-loader` 转换一下

对了，loader执行顺序是从下往上的！也就是说，在上面这个例子中，先执行`css-loader`，再执行`raw-loader`。

+ 内联方式（少用）

当然，也可以在每个import语句中显示指定loader，这样就不用再在`webpack.config.js` 中进行配置

```javascript
import Styles from 'style-loader!css-loader?modules!./styles.css';
```



#### 插件

不同于转换某些文件类型的`loader` ，插件可以用于更大范围的任务，比如： 打包优化，资源管理……

因此，插件的主要目的就是实现 `loader`无法实现的其他功能。

##### 配置

需要使用一个插件，只需要先 `require` ，再在`plugins` 中添加

```javascript
//webpack.config.js

const HtmlWebpackPlugin = require('html-webpack-plugin')
module.exports = {
  plugnis: [
    new HtmlWebpackPlugin({ template: './src/index.html'}),
    new webpack.ProgressPlugin(),//用于自定义在编译过程中的进度报告
  	
  ],
};
```

上述代码生成一个HTML文件，再将所有的bundle注入到此文件中。

必须传入一个new实例，因此这样才可以使插件传入参数或者选项。







#### 模式

选择不同的模式，在相应环境下进行打包

##### 配置

```javascript
module.exports = {
  mode:'production', // development/production/none三种模式选一
};
```



### 模块

开发者将程序分解为一个个功能离散的`chunk` ，称之为模块。

可以使用loader扩展webpack处理模块的能力：`loader` 告诉webpack如何处理非原生模块，并将相关依赖引入到`bundles` 中。

常见loader：

+ TypeScript
+ Sass
+ Less



#### 模块解析

`resolver`库帮助寻找模块绝对路径，一个模块可以作为另一个模块的依赖模块并被引用，例如

```javascript
import xxx from 'xxx/module';
//或者
require('.../module');
```

##### 解析规则

+ 绝对路径

```javascript
import '/home/me/file';

import 'C:\\Users\\me\\file';
```

+ 相对路径

```javascript
import '../src/file1';
import './file2';
```

当前文件的目录被认为是上下文目录，会自动进行拼接

+ 模块路径

```javascript
import 'module';
import 'module/lib/file';
```

会在`resolve.modules` 指定的目录自动检索模块 



### 依赖图

每当一个文件依赖另一个文件时，`webpack` 都会将文件视为直接存在 **依赖关系**。这使得 `webpack` 可以获取非代码资源，如 images 或 web 字体等。并会把它们作为 *依赖* 提供给应用程序。

当 `webpack` 处理应用程序时，它会根据命令行参数中或配置文件中定义的模块列表开始处理。 从 [*入口*](https://webpack.docschina.org/concepts/entry-points/) 开始，webpack 会递归的构建一个 *依赖关系图*，这个依赖图包含着应用程序中所需的每个模块，然后将所有模块打包为少量的 *bundle* —— 通常只有一个 —— 可由浏览器加载。

> 以上摘自webpack[官方文档](https://webpack.docschina.org/concepts/dependency-graph/)。



### mainifest

`runtime` 与 `mainifest`管理所有模块的交互。

#### runtime

`runtime`，以及伴随的 `manifest` 数据，主要是指：在浏览器运行过程中，`webpack` 用来连接模块化应用程序所需的所有代码。它包含：在模块交互时，连接模块所需的加载和解析逻辑。包括：已经加载到浏览器中的连接模块逻辑，以及尚未加载模块的延迟加载逻辑。

> 以上摘自webpack[官方文档](https://webpack.docschina.org/concepts/manifest/)。
