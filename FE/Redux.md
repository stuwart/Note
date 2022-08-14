## Redux

> 感谢[官方文档](https://cn.redux.js.org/) ,本文例子均采用官方文档。

### 概述

#### Redux是什么？

​		Redux与Vuex一样，是一种状态管理工具。它帮助开发者管理全局状态，比如某个数据在整个应用中多次使用到，可能还会发生更新，这种时候如何手写一个一个的更新函数显得极为繁琐，这时候使用`Redux` 之类的状态管理工具，就能够极大提高效率，并且提高代码的可读性和扩展性。

##### 合适的适用范围

+ 在应用的大量地方，都存在大量的状态
+ 应用状态会随着时间的推移而频繁更新
+ 更新该状态的逻辑可能很复杂
+ 中型和大型代码量的应用，很多人协同开发

#### Redux套件

+ React-Redux ：在React中使用`Redux`
+ Redux Toolkit：官方推荐的`Redux` 编写方法
+ Redux Devtools 



### 概念

#### State

在普通的React组件中，例如一下例子：

```react
function Counter() {
  // State: a counter value
  const [counter, setCounter] = useState(0)

  // Action: 当事件发生后，触发状态更新的代码
  const increment = () => {
    setCounter(prevCounter => prevCounter + 1)
  }

  // View: UI 定义
  return (
    <div>
      Value: {counter} <button onClick={increment}>Increment</button>
    </div>
  )
}
```



包含了三部分：

+ State：局部数据的数据源
+ View： 为用户展示的视图
+ Actions: 事件，用于更新state状态

**以下为Redux官方中绘出的单向数据驱动视图示意图**

![image.png](https://s2.loli.net/2022/08/12/sk91EhNoVrdBzjL.png)





#### Immutability不可变性

​	Redux要求**所有的状态更新都使用不可变的方式**，即先复制一份原数据，再在该副本上进行修改。



### 术语

#### Action

`action`是一个对象，具有`type`字段。

`type`字段用于给该`action`一个描述，是**“域/事件名称”**的格式。

例如`todos/todoAdded`，第一部分`todos`为域，是这个action所属的特征或者类别，第二部分`todoAdded`是发生的具体事件。

一般可以将需要附加的其他信息放在`payload`字段中。

```javascript
const addTodoAction = {
  type: 'todos/todoAdded',
  payload: 'Buy milk'
}
```



#### Acation Creator

动作生成器，用于创建并返回一个action，用生成器就不用每次都手写一个`action`了。

```javascript
const addTodo = text => {
  return {
    type: 'todos/todoAdded',
    payload: text
  }
}
```



#### Reducer

一个函数，具有`state` 和`action` 对象，决定如何更新状态并返回新状态。

形式： `(state,action) => newState` 

可以理解为一个事件监听器，根据接收到的`action`来对状态进行相应的处理。

##### 规范

+ 仅使用 `state` 和 `action` 参数计算新的状态值
+ 禁止直接修改 `state`。必须通过复制现有的 `state` 并对复制的值进行更改的方式来做 *不可变更新（immutable updates）*。
+ 禁止任何异步逻辑、依赖随机值或导致其他“副作用”的代码

⚠️遵守这些规范的主要原因是为了保持代码的稳定，使得开发者能够确认在调用了之后能够确认输出结果。

```javascript
const initialState = { value: 0 }

function counterReducer(state = initialState, action) {
  // 检查 reducer 是否关心这个 action
  if (action.type === 'counter/increment') {
    // 如果是，复制 `state`
    return {
      ...state,
      // 使用新值更新 state 副本
      value: state.value + 1
    }
  }
  // 返回原来的 state 不变
  return state
}
```



#### Store

和Vuex中的概念差不多，所有的状态存在于`Store`中。

通过传入一个`Reducer` 来创建，如果要读取状态，可以用`store.getState()` 方法

```javascript
import { configureStore } from '@reduxjs/toolkit'

const store = configureStore({ reducer: counterReducer })

console.log(store.getState())
// {value: 0}
```



#### Dispatch

这是更新状态的唯一方法，通过调用`store.dispatch(action)` ，调用后`store`将执行所有的`reducer`（事件监听器）计算出更新后的`state` ，然后调用 `getState` 获取新的`state`

```javascript
store.dispatch({ type: 'counter/increment' })

console.log(store.getState())
// {value: 1}
```



#### Selector

这是一个函数，用于在`store`中获取指定的数据，在大型应用中可以提升性能，避免重复的读取逻辑。

```javascript
const selectCounterValue = state => state.value

const currentValue = selectCounterValue(store.getState())
console.log(currentValue)
// 2
```





### 实现

目录结构：

+ `/src`
  + `index.js`
  + `App.js`
  + `/app`
    + `store.js`
  + `/features`
    + `/counter`
      + `Counter.js`
      + `counterSlice.js`



#### 创建Store

使用 `Redux Toolkit` 的`configureStore`，传入一个**reducer**



##### Redux Slice

**“切片”是应用中单个功能的 Redux reducer 逻辑和 action 的集合**, 通常一起定义在一个文件中。该名称来自于将根 Redux 状态对象拆分为多个状态“切片”。



Store配置举例：

```javascript
import { configureStore } from '@reduxjs/toolkit'
import usersReducer from '../features/users/usersSlice'
import postsReducer from '../features/posts/postsSlice'
import commentsReducer from '../features/comments/commentsSlice'

export default configureStore({
  reducer: {
    users: usersReducer,
    posts: postsReducer,
    comments: commentsReducer
  }
})
-------------------------------------------------------

//可以使用combineReducers 来合并slice
const rootReducer = combineReducers({
  users: usersReducer,
  posts: postsReducer,
  comments: commentsReducer
})

//然后store配置可以改为：
const store = configureStore({
  reducer: rootReducer
})
```



#### 创建Slice Reducer 和 Action

`Slice` 文件：

```javascript
import { createSlice } from '@reduxjs/toolkit'

export const counterSlice = createSlice({
  name: 'counter',  //作为action的第一部分
  initialState: {
    value: 0
  },
  reducers: {
    increment: state => {  //increament作为action的第二部分
      state.value += 1
    },
    decrement: state => {
      state.value -= 1
    },
    incrementByAmount: (state, action) => {
      state.value += action.payload
    }
  }
})

export const { increment, decrement, incrementByAmount } = counterSlice.actions

export default counterSlice.reducer
```



可以使用`createSlice`函数生成`action`。它接受一个**name**参数，作为`action`类型的第一部分，每个`reducer`函数的键名作为第二部分。

查看/调用action：

```javascript
console.log(counterSlice.actions.increment())
// {type: "counter/increment"}
```



#### Immer

`createSlice` 内部使用了`Immer` 库，不用再手写繁琐的不可变更新了，极大简化了代码，并且提高了可读性。例如：

```javascript
function handwrittenReducer(state, action) {
  return {
    ...state,
    first: {
      ...state.first,
      second: {
        ...state.first.second,
        [action.someId]: {
          ...state.first.second[action.someId],
          fourth: action.someValue
        }
      }
    }
  }
}


---------------------------------------------------
  //更改后
function reducerWithImmer(state, action) {
  state.first.second[action.someId].fourth = action.someValue
}
```







#### Thunk

不论是`dispatch action` 还是`store`调用`reducer` ，都是同步操作，如果需要进行异步编程，则需要使用**`thunk`** 函数。

**`Thunk`**函数由两个函数组成：

+ 一个内部`thunk` 函数，以`dispatch` 和`getState` 作为参数
+ 外部创建者函数，创建并返回`thunk`函数



实例：

```javascript
// 外部的 thunk creator 函数
const fetchUserById = userId => {
  // 内部的 thunk 函数
  return async (dispatch, getState) => {
    try {
      // thunk 内发起异步数据请求
      const user = await userAPI.fetchById(userId)
      // 但数据响应完成后 dispatch 一个 action
      dispatch(userLoaded(user))
    } catch (err) {
      // 如果过程出错，在这里处理
    }
  }
}
```



#### 在组件中使用

由于组件无法与`store`通信，因此需要使用特定的hook来调用相关方法。

使用`useSelector` 获取数据

```react
const count = useSelector(selectCount);
//使用该hook来调用selector函数，这样组件就可以与store通信。
```

使用`useDispatch` 来执行更新

```react
const dispatch = useDispatch();

//使用
onClick = { () => dispatch(increment())}
```





#### 在App中配置

```react
import { Provider } from 'react-redux'
ReactDOM.render(
  <Provider store={store}>
    <App />
  </Provider>,
  document.getElementById('root')
)
```

使用`<Provider>` 包裹 `<App>` ，在幕后传递`store`，这样子组件使用`Hook`就能够知道与哪个`store` 进行通信了！













