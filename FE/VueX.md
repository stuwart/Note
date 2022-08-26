## VueX

> 感谢[官方文档](https://v3.vuex.vuejs.org/zh/#什么是-状态管理模式),本文示例以及部分章节为原文内容。

本文主要在学习了状态管理中`Redux` 和`Vuex` 之后，对`Vuex` 进行复习归纳。

`VueX`与`Redux` 很多地方是相似的，比如有一个`store` ，有`state`,`mutation` ，但是`action` 的概念不太一样，会在稍后进行解释。

因此，本文更确切的说是`Vuex`与`Redux`的对比。



### 最简单的示例

```js
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)

const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  }
})

//获取数据
store.state

//进行状态更新
store.commit('increment')
```









### 核心概念

#### State

##### 单一状态树

`Vuex` 使用单一状态树，即一个对象就包含了整个应用的状态，是作为唯一数据源的存在。而这个状态树，即是`store` 的实例，并且这个单一状态树也能够让开发者在调试过程中随时取得整个应用的快照。



##### mapState

使用`mapState` 函数使得代码变得简洁。

否则如果你在一个组件内获取多个状态的话，得把每个状态写一个计算属性。但是用了**mapState** 函数，就会很简洁。

⚠️ ：在`Redux` 中，可以使用`Selector` 来减少重复获取相同的值所带来的性能消耗。

```js
//有三种传参方式

computed: mapState({
    // 箭头函数可使代码更简练
    count: state => state.count,

    // 传字符串参数 'count' 等同于 `state => state.count`
    countAlias: 'count',

    // 为了能够使用 `this` 获取局部状态，必须使用常规函数
    countPlusLocalState (state) {
      return state.count + this.localCount
    }
  })


//如果计算属性名称与要获取的state的名称一样
computed: mapState([
  // 映射 this.count 为 store.state.count
  'count'
])
```



如果这样的话，就不能再设置别的计算属性了，因此可以使用**对象展开运算符** 将其他计算属性 与 获取**store**状态的属性一起用。

```js
computed: {
  localComputed () { /* ... */ },
  // 使用对象展开运算符将此对象混入到外部对象中
  ...mapState({
    // ...
  })
}
```



#### Getter

类似于`Redux` 中的 `Reducer`

直接访问`state`中的数据，获得的就是原数据，而要想获得的返回值是对原数据进行一定操作的，比如原数据+1之类，那么就需要用到`Getter`

**Getter** 接受`state` 作为第一个参数

```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```

也接受其他`getter` 作为第二个参数

```js
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
```



###### 属性访问

可以直接通过属性访问，例如

```js
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }]
```



##### 通过方法访问

通过让`getter` 返回一个函数，来实现给`getter` 传递参数。如果你需要对`store` 里的数组查询，这会很有用

```js
getters: {
  // ...
  getTodoById: (state) => (id) => {
    return state.todos.find(todo => todo.id === id)
  }
}


//访问
store.getters.getTodoById(2) // -> { id: 2, text: '...', done: false }
```



##### mapGetters

同`mapState`

```js
export default {
  // ...
  computed: {
  // 使用对象展开运算符将 getter 混入 computed 对象中
    ...mapGetters([
      'doneTodosCount',
      'anotherGetter',
      
      //也可以这么写，
      //把 `this.doneCount` 映射为 `this.$store.getters.doneTodosCount`
       doneCount: 'doneTodosCount'
      // ...
    ])
  }
}
```





#### Mutations

对应`Redux`中的`dispatch` 

每个**mutation** 有一个字符串类型的**type** 和一个回调函数。

回调函数就是我们对状态进行更改的地方

##### 参数

这个回调函数会接受`state` 作为第一个参数

```js
const store = new Vuex.Store({
  state: {
    count: 1
  },
  mutations: {
    increment (state) {
      // 变更状态
      state.count++
    }
  }
})

//调用
store.commit('increment');
```

回调函数可以接受额外的参数，称作**载荷**

```jsx
mutations: {
  increment (state, payload) {
    state.count += payload.amount
  }
}

//调用
store.commit('increment', {
  amount: 10
})
```



##### 对象风格提交方式

可以把`commit` 的参数写作一个对象，把`mutations`名作为`type`字段。 而`mutations` 函数不需要做任何变化。

```js
store.commit({
  type: 'increment',
  amount: 10
})
```





#### Actions

`Actions` 类似于**Redux** 中的 `Thunk` ,它类似于`mutation`,不同之处在于：

+ Action 提交的是 mutation，而不是直接变更状态。
+ Action 可以包含任意异步操作。

一个简单的示例：

```jsx
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
    
    //用参数解构的写法
    increment ({ commit }) {
    	commit('increment')
  	}
  }
})

//调用
store.dispatch('increment')
```

##### 参数

**Action** 接受一个与	`store` 实例具有相同方法、属性的	`context`对象，因此可以直接类似这样访问`context.state` 。 但是它并非`store` 本身！



##### 特性

Action 与`mutation`最大的不同是支持**异步操作**

```jsx
actions: {
  incrementAsync ({ commit }) {
    setTimeout(() => {
      commit('increment')
    }, 1000)
  }
}

// 以载荷形式分发
store.dispatch('incrementAsync', {
  amount: 10
})

// 以对象形式分发
store.dispatch({
  type: 'incrementAsync',
  amount: 10
})
```



##### mapActions

类似于之前的**map...**  函数

```js
methods: {
    ...mapActions([
      'increment', // 将 `this.increment()` 映射为 `this.$store.dispatch('increment')`

      // `mapActions` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.dispatch('incrementBy', amount)`
    ]),
    ...mapActions({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.dispatch('increment')`
    })
```



##### 组合Action

请理解，`store.dispatch` 可以处理一个被触发的action的函数返回的`promise` ，并且，`store.dispatch` 仍然返回**Promise** 。

```js
actions: {
  actionA ({ commit }) {
    return new Promise((resolve, reject) => {
      setTimeout(() => {
        commit('someMutation')
        resolve()
      }, 1000)
    })
  }
}

//调用
store.dispatch('actionA').then(() => {
  // ...
})


//另一个action可以写：
actions: {
  // ...
  actionB ({ dispatch, commit }) {
    return dispatch('actionA').then(() => {
      commit('someOtherMutation')
    })
  }
}
```



如果利用`async/await`:

```js
// 假设 getData() 和 getOtherData() 返回的是 Promise

actions: {
  async actionA ({ commit }) {
    commit('gotData', await getData())
  },
  async actionB ({ dispatch, commit }) {
    await dispatch('actionA') // 等待 actionA 完成
    commit('gotOtherData', await getOtherData())
  }
}
```





#### Module

如果应用很大， 可以分割成多个模块，避免全部写在一个文件内，这样会很臃肿，而且不易维护。

例如这样

```js
const moduleA = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: () => ({ ... }),
  mutations: { ... },
  actions: { ... }
}

const store = new Vuex.Store({
  modules: {
    a: moduleA,
    b: moduleB
  }
})

store.state.a // -> moduleA 的状态
store.state.b // -> moduleB 的状态
```



##### 局部状态

模块内部的`mutation`和`getter` 接受 的第一个参数是**模块的局部状态对象（state）**

```js
const moduleA = {
  state: () => ({
    count: 0
  }),
  mutations: {
    increment (state) {
      // 这里的 `state` 对象是模块的局部状态
      state.count++
    }
  },

  getters: {
    doubleCount (state) {
      return state.count * 2
    }
  }
}
```



同样，对于模块内部的`action`，局部状态通过 `context.state` 暴露出来，根节点状态则为 `context.rootState`：

```js
const moduleA = {
  // ...
  actions: {
    incrementIfOddOnRootSum ({ state, commit, rootState }) {
      if ((state.count + rootState.count) % 2 === 1) {
        commit('increment')
      }
    }
  }
}
```

对于模块内部的 `getter`，根节点状态会作为第三个参数暴露出来：

```js
const moduleA = {
  // ...
  getters: {
    sumWithRootCount (state, getters, rootState) {
      return state.count + rootState.count
    }
  }
}
```



##### 命名空间

可以通过添加	`namespaced:true` 使这个模块成为**带命名空间的模块**，当模块被注册后，它的所有`getter`,`action` 和`mutation`都会根据模块的注册路径自动调整命名,这样子在嵌套的模块内部调用就只会调用模块内部的`getter`等，而不会注册在全局中。

```js
const store = new Vuex.Store({
  modules: {
    account: {
      namespaced: true,

      // 模块内容（module assets）
      state: () => ({ ... }), // 模块内的状态已经是嵌套的了，使用 `namespaced` 属性不会对其产生影响
      getters: {
        isAdmin () { ... } // -> getters['account/isAdmin']
      },
      actions: {
        login () { ... } // -> dispatch('account/login')
      },
      mutations: {
        login () { ... } // -> commit('account/login')
      },

      // 嵌套模块
      modules: {
        // 继承父模块的命名空间
        myPage: {
          state: () => ({ ... }),
          getters: {
            profile () { ... } // -> getters['account/profile']
          }
        },

        // 进一步嵌套命名空间
        posts: {
          namespaced: true,

          state: () => ({ ... }),
          getters: {
            popular () { ... } // -> getters['account/posts/popular']
          }
        }
      }
    }
  }
})
```



##### 命名空间内访问全局内容

如果开启了命名空间，还想访问全局内容，那么可以使用`rootState` 和`rootGetters` 作为第三和第四参数传入`getter`，也会通过`cnotext` 对象的属性传入`action`.

若需要在全局命名空间内分发 action 或提交 mutation，将 `{ root: true }` 作为第三参数传给 `dispatch` 或 `commit` 即可。

```js
modules: {
  foo: {
    namespaced: true,

    getters: {
      // 在这个模块的 getter 中，`getters` 被局部化了
      // 你可以使用 getter 的第四个参数来调用 `rootGetters`
      someGetter (state, getters, rootState, rootGetters) {
        getters.someOtherGetter // -> 'foo/someOtherGetter'
        rootGetters.someOtherGetter // -> 'someOtherGetter'
      },
      someOtherGetter: state => { ... }
    },

    actions: {
      // 在这个模块中， dispatch 和 commit 也被局部化了
      // 他们可以接受 `root` 属性以访问根 dispatch 或 commit
      someAction ({ dispatch, commit, getters, rootGetters }) {
        getters.someGetter // -> 'foo/someGetter'
        rootGetters.someGetter // -> 'someGetter'

        dispatch('someOtherAction') // -> 'foo/someOtherAction'
        dispatch('someOtherAction', null, { root: true }) // -> 'someOtherAction'

        commit('someMutation') // -> 'foo/someMutation'
        commit('someMutation', null, { root: true }) // -> 'someMutation'
      },
      someOtherAction (ctx, payload) { ... }
    }
  }
}
```

##### 在带命名空间的模块注册全局 

##### action

若需要在带命名空间的模块注册全局 action，你可添加 `root: true`，并将这个 action 的定义放在函数 `handler` 中。例如：

```js
{
  actions: {
    someOtherAction ({dispatch}) {
      dispatch('someAction')
    }
  },
  modules: {
    foo: {
      namespaced: true,
      actions: {
        someAction: {
          root: true,
          handler (namespacedContext, payload) { ... } // -> 'someAction'
        }
      }
    }
  }
}
```

##### 带命名空间的绑定函数

当使用 `mapState`, `mapGetters`, `mapActions` 和 `mapMutations` 这些函数来绑定带命名空间的模块时，写起来可能比较繁琐：

```js
computed: {
  ...mapState({
    a: state => state.some.nested.module.a,
    b: state => state.some.nested.module.b
  })
},
methods: {
  ...mapActions([
    'some/nested/module/foo', // -> this['some/nested/module/foo']()
    'some/nested/module/bar' // -> this['some/nested/module/bar']()
  ])
}
```

对于这种情况，你可以将模块的空间名称字符串作为第一个参数传递给上述函数，这样所有绑定都会自动将该模块作为上下文。于是上面的例子可以简化为：

```js
computed: {
  ...mapState('some/nested/module', {
    a: state => state.a,
    b: state => state.b
  })
},
methods: {
  ...mapActions('some/nested/module', [
    'foo', // -> this.foo()
    'bar' // -> this.bar()
  ])
}
```

而且，你可以通过使用 `createNamespacedHelpers` 创建基于某个命名空间辅助函数。它返回一个对象，对象里有新的绑定在给定命名空间值上的组件绑定辅助函数：

```js
import { createNamespacedHelpers } from 'vuex'

const { mapState, mapActions } = createNamespacedHelpers('some/nested/module')

export default {
  computed: {
    // 在 `some/nested/module` 中查找
    ...mapState({
      a: state => state.a,
      b: state => state.b
    })
  },
  methods: {
    // 在 `some/nested/module` 中查找
    ...mapActions([
      'foo',
      'bar'
    ])
  }
}
```





















