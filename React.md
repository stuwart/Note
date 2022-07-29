## React



### 渲染

HTML：

```html
<div id="root">
  
</div>
```



JavaScript:

```react
const element = <h1>Hello,world</h1>;

ReactDOM.render(
  element,
  document.getElementById('root')
);
```





### 组件

#### 类组件

```react
class Welcome extends React.Component{
  render(){
    return <h1>Hello,{this.props.name}</h1>
  }
}
```



#### 函数组件

```react
function Welcome(props)
{
  return <h1>Hello,{props.name}</h1>
}

使用：
const element = <Welcome name="HY" />

```



#### 函数组件转化类组件

1.创建一个同名的 class ，并 继承于 `React.Component` 

2. 添加一个空的 `render()` 方法，并将函数体移入
3. 将原来的 `props` 转化为`this.props`
4. 删除剩余的空函数





### State

State 与 props 相似，但是 state 是私有的，受控于当前组件。



#### 添加构造函数

```react
class Clock extends React.Component{
  constructor(props){
    super(props);
    this.state = {
      date : new Date()
    };
  }
}
```



#### 调用

```react
在render中：
//原来：  this.props.date
this.state.date
```



#### 修改

```react
this.setState(
	{
    comment:'Hello'
  }
);
```



#### State的更新可能是异步的

**构造函数是唯一可以给 this.state 赋值的地方**



#### State的更新会被合并







### 生命周期

#### 生命周期方法

##### componentDidMount

在组件被渲染到DOM中后运行



##### componentWillUnmount



#### 调用顺序

##### 挂载

+ `constructor()`
+ `static getDerivedStateFirmProps()`
+ `render()`
+ `componentDidMount()`

##### 更新

+ `static getDerivedStateFormProps()`
+ `shouldComponentUpdate()`
+ `render()`
+ `getSnapshptBeforeUpdate()`
+ `componentDidUpdate()`

##### 卸载

+ `componentWillUnmount()`

##### 错误处理

+ `static getDerivedStateFormError()`
+ `componentDidCatch()`





### 事件处理

#### 形式

 `on + 事件名称 = { ()=>{}  }` 

#### 获取事件对象

+ 通过事件处理程序的参数获取事件对象e





#### 阻止默认行为

```react
function Form()
{
  function handleSubmit(e)
  {
    e.preventDefault();
  }
  
  return {
    //DOM元素
    <form onSubmit={handleSubmit}></form>
  }
}
```





















