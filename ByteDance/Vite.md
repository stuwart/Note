## Vite

### 浅谈构建工具

同学们可能在平时用构建工具时并没有想过我们为什么要用这些工具，这是因为前端工程有许多资源，比如：

+ JavaScript
+ TypeScript
+ JavaScript XML
+ CSS
+ SCSS

等等.....

面对这么多模块，前端工程具有一些明显的特点：

+ 模块化：要把不同的模块进行分开设计，提高代码的可复用性
+ 资源编译：一些高级语法需要进行编译
+ 产物质量：在实际开发中，代码体积、代码性能都是需要考虑到的因素，相差很少便会影响到用户体验
+ 开发效率：需要提高开发效率，在完成一部分开发后马上就能看到进度



#### 意义

[![vyAgoj.png](https://s1.ax1x.com/2022/08/21/vyAgoj.png)](https://imgse.com/i/vyAgoj)

#### 现存问题

目前的主流构建工具主要有：

[![vyA4S0.png](https://s1.ax1x.com/2022/08/21/vyA4S0.png)](https://imgse.com/i/vyA4S0)

目前存在的两个很大问题在于：

+ 启动慢，项目编译耗时长
+ 热更新慢，修改代码后不能实时更新

瓶颈主要在于：

+ `budnle` 带来的性能开销

+ `JavaScript` 语言的性能瓶颈



#### 趋势

##### 浏览器对原生ESM的支持

两大要素：

+ script标签增加	`type = "module"`  属性
+ 使用ESM模块导入导出语法



优势：

+ 无需打包项目源代码，减少了`bundle` 打包的开销
+ 天然的按需加载
+ 可以利用文件级的浏览器缓存



##### 基于原生语言编写前端编译构建工具链

如`Go` 的`Esbuild` 和`Rust` 的`SWC`

`Esbuild` 具备以下能力：

+ 打包器 `Bundler`
+ 编译器 `Transformer`
+ 压缩器 `Minifier`





### 概要介绍

#### 概览

说了这么久，那么`Vite` 究竟是什么呢？

`Vite` ，是**新一代前端构建工具**

有**两大组成部分**：

+ No-bundle开发服务，源文件无需打包
+ 生产环境基于Rollup和Bundler

有**两大核心特征**：

+ 高性能!`dev` 的启动速度和热更新速度非常快
+ 简单易用，开发者体验好



#### Vite自带集成功能

[![vyALk9.png](https://s1.ax1x.com/2022/08/21/vyALk9.png)](https://imgse.com/i/vyALk9)



### 上手实战

#### 项目初始化

[![vyAOYR.png](https://s1.ax1x.com/2022/08/21/vyAOYR.png)](https://imgse.com/i/vyAOYR)



更多操作指南建议观看官方文档。

### 整体架构

[![vyEFkd.png](https://s1.ax1x.com/2022/08/21/vyEFkd.png)](https://imgse.com/i/vyEFkd)



#### 关键技术

##### 依赖预打包

为什么❓

+ 避免`node_modules` 过多的文件请求
+ 将`CommonJS` 格式转换为`ESM` geshi 



🌟实现原理

+ 服务启动前扫描代码中用到的依赖
+ 用`Esbuild` 对依赖代码进行预打包
+ 改写`import`语句，指定依赖为构建产物路径

```js
//改写前
import React from "react"

//改写后
import React from "/node_modules/,vite/tract.js"
```



##### 单文件编译

使用`Esbuild` 编译 TS/JSX

优势： 编译速度大大提升

劣势：

+ 不支持类型检查
+ 不支持语法降级到ES5



##### 代码压缩

进行压缩后性能可以得到较大的提升。



##### 插件机制

开发阶段： 模拟`Rollup` 插件机制

生产环境： 直接食用`Rollup`



### 进阶路线

#### 深入双引擎

+ Esbuild
+ Rollup

**推荐学习顺序**

1. 了解其基本使用
2. 动手尝试各项常用配置
3. 学习其插件开发



#### Vite插件开发

为什么需要插件机制？

+ 抽离核心逻辑
+ 易于拓展



常见钩子：



[![vyEAfI.png](https://s1.ax1x.com/2022/08/21/vyEAfI.png)](https://imgse.com/i/vyEAfI)

**插件示例**

[![vyEVpt.png](https://s1.ax1x.com/2022/08/21/vyEVpt.png)](https://imgse.com/i/vyEVpt)



复杂度较低：`JSON`加载插件

复杂度中等：`Esbuild` 接入插件

复杂度较高：官方React插件















