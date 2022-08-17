## React进阶



### 代码分割

随着应用体积变大，代码包也会随之增长，打包后的文件可能会非常大，因此使用**代码分割**技术就很有必要了。

使用`Webpack` 等构建打包工具，能够进行分割，创建多个包并在运行时动态加载。进行代码分割能够进行**“懒加载”**用户所需要的内容，同时避免加载那些用户不会用到的内容。



#### import

最佳引入代码分割的方式便是`import()` 语法。

```js
//代码分割前
import { add } from './math';

console.log(add(16, 26)); 


//代码分割后
import("./math").then(math => {
  console.log(math.add(16, 26));
});
```

使用这种方式**动态引入** ，可以使`Webpack`解析该语法并自动分割。



#### React.lazy

该函数可以像渲染常规组件一样处理动态导入，它接受一个函数，需要动态调用`import`，并返回一个`Promise` ，这个`Promise` 需要Resolve 一个`default export` 的组件。 

```jsx
//使用之前
import OtherComponent from './OtherComponent';

//使用之后
const OtherComponent = React.lazy(() => import('./OtherComponent'));
```









































































