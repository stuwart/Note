## Vue进阶

### 组件注册

#### 组件名称

一般采用两种方式

**kebab-case**：`my-component-a`

**PascalCase**:`MyComponentA`



#### 组件注册

##### 局部注册

```js
var ComponentA = { /* ... */ }//创建一个组件对象


new Vue({
  el: '#app',
  components: {
    'component-a': ComponentA,
    'component-b': ComponentB
  }
})
```



##### 在模块系统中局部注册

```js
//ComponentB.js
import ComponentA from './ComponentA'
import ComponentC from './ComponentC'

export default {
  components: {
    ComponentA,
    ComponentC
  },
  // ...
}
```



#### Prop

##### 名称规范

在`js` 和`vue` 文件的模版（template）中，使用**驼峰命名法**，然而在HTML中需要使用**短横线分隔** 。这是因为`HTML` 对大小写不敏感。

##### 类型

```js
props: {
  title: String,
  likes: Number,
  isPublished: Boolean,
  commentIds: Array,
  author: Object,
  callback: Function,
  contactsPromise: Promise // or any other constructor
}
```



**传入所有对象**

如果一个对象中属性很多，写起来会很复杂，可以使用语法

`v-bind = "xxx"`,这样就可以一下子传入`xxx` 具有的所有参数。



##### 单向数据流

与**React**中`Prop` 一样，父传给子的数据不允许被修改，这是利于维护的。要修改可以变为组件自身的`data`，这像是**React** 中的`State`



##### 验证

可以在传入`prop`时进行验证，若不符合要求控制台会进行警告。

```js
Vue.component('my-component', {
  props: {
    // 基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组默认值必须从一个工厂函数获取
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].includes(value)
      }
    }
  }
})
```

##### 类型检查

**type**可能存在的值：

+ **String**
+ **Number**
+ **Boolean**
+ **Array**
+ **Object**
+ **Date**
+ **Function**
+ **Symbol**
+ 自定义函数

自定义函数例如：

```js
function Person (firstName, lastName) {
  this.firstName = firstName
  this.lastName = lastName
}
```



#### 非prop属性

​		指没有显示声明prop的属性。这种时候，属性会自动被添加到组件的根元素上。

##### 自动合并

若原来已经存在`class` 和`style`属性，那么新添加的值会被自动合并，而不是覆盖。

##### 禁用属性传承

不希望根元素继承属性，可以添加`inheritAttrs:false`

```js
Vue.component('base-input', {
  inheritAttrs: false,
  props: ['label', 'value'],
  template: `
    <label>
      {{ label }}
      <input
        v-bind="$attrs"  
        v-bind:value="value"
        v-on:input="$emit('input', $event.target.value)"
      >
    </label>
  `
})
```



上述例子中，若没有`v-bind =$attrs` 则传递的非prop属性会自动添加到`label` 标签。







### 边界情况

该节主要对`Vue` 中涉及到的一些小细节和规则进行说明。

#### 访问元素/组件

​		在`Vue`中，我们一般不手动操作DOM元素，也不会干涉一个组件实例的内部，但是如果一定要这么做，也是可以的，接下来对几种可能的情况进行讨论：

##### 访问根实例

​		在`new Vue` 实例的子组件中，要访问根实例，可以通过`$root` 属性进行访问，例如：

```javascript
new Vue({
  data: {
    foo: 1
  },
  computed: {
    bar: function () { /* ... */ }
  },
  methods: {
    baz: function () { /* ... */ }
  }
})

//使用方法
this.$root.foo  //获取
this.$root.foo = 2 //写入
this.$root.bar //访问计算属性
this.$root.baz()  //调用根组件方法
```



##### 访问父级组件实例

​	`$parent`属性 ，用法与根组件一样。

用这种方式也可以替代使用`prop`父传子的数据传输方式。



##### 访问子组件实例

​		使用`ref` 与子组件建联。

```html
<base-input ref="usernameInput"></base-input>
```

定义了`ref` 之后，在`base-input`组件内，可以使用`this.$ref.usernameInput` 来访问该组件实例。



##### 依赖注入

​		如果有多个组件存在嵌套，那么直接使用`$parent` 会不易维护，这种情况下推荐使用**依赖注入**，涉及到`provide` 和`inject`。

**provide**：允许我们指定想给后代组件提供的数据/方法。

```js
provide:function(){
  return {
    getMap: this.getMap //将父组件中的getMap传出去
  }
}
```

​	设定完后，可以在任何后代组件使用`inject`来接收。

```js
inject: ['getMap']  //后代组件接收父组件中传来的getMap
//可以使用this.getMap来调用。
```





#### 程序化事件监听

+ 监听一个事件： `$on(事件名，事件处理函数)`
+ 一次性监听一个事件：`$once(事件名，事件处理函数)`
+ 停止监听一个事件：`$off(事件名，事件处理函数)`



```js
mounted: function () {
  var picker = new Pikaday({
    field: this.$refs.input,
    format: 'YYYY-MM-DD'
  })

  this.$once('hook:beforeDestroy', function () {
    picker.destroy()
  })
}
```



#### 循环引用

循环引用的组件个人认为使用较少，也没有特别大的实用价值，因此暂时不作记录，日后再更。



#### 控制更新

可以在根元素上使用`v-once`来创建低开销的静态组件。若一个组件内含有大量静态元素，那么可以使用这种方法，使得加载完成后被缓存起来，下次就不用再被加载了。可以提高速度。

```js
Vue.component('my-component', {
  template: `
    <div v-once>
      <h1>Terms of Service</h1>
      ... a lot of static content ...
    </div>
  `
})
```





### Mixin

**混入 (mixin)** 提供了一种非常灵活的方式，来分发 Vue 组件中的可复用功能。一个混入对象可以包含任意组件选项。当组件使用混入对象时，所有混入对象的选项将被“混合”进入该组件本身的选项。





#### 合并

若组件内部与混入对象有重名的选项时，会以适当的方式进行

##### 数组

不重名的自动合并，重名的以组件原先有的数据为准。

##### 钩子函数

混入对象的钩子函数先执行，组件中的后执行

##### 选项

如`methods` ,	`components` ,	`directives` ，以组件中的数据优先进行合并。



#### 自定义选项合并策略

​		自定义选项将使用默认策略，即简单地覆盖已有值。如果想让自定义选项以自定义逻辑合并，可以向 `Vue.config.optionMergeStrategies` 添加一个函数：

```js
Vue.config.optionMergeStrategies.myOption = function (toVal, fromVal) {
  // 返回合并后的值
}
```

对于多数值为对象的选项，可以使用与 `methods` 相同的合并策略：

```js
var strategies = Vue.config.optionMergeStrategies
strategies.myOption = strategies.methods
```





### 自定义指令

#### 语法

**注册全局指令**

```js
Vue.directive('focus',{
  inserted:function(el){
    el.focus();
  }
})
```

**注册局部指令**

```js
directives:{
  focus:{
    inserted:function(el){
      el.focus();
    }
  }
}
```



注册之后便可以使用 `v-focus` 指令。

#### 钩子函数

自定义指令定义对象中提供以下几种钩子函数



+ `bind`：只调用一次，第一次绑定到指令时调用。
+ `inserted` ：被绑定元素插入父节点时调用
+ `update` ：所在组件的`VNode`更新时调用（有可能是更新前，也有可能是更新后）

+ `componentUpdated`：指令所在组件的`VNode` 及其子VNode全部更新后调用
+ `unbind` ：只调用一次，指令与元素解绑时调用



##### 参数

+ el：指令所绑定的元素，用于直接操作DOM
+ **binding**：一个对象，包含：
  + `name`
  + `value`
  + `oldValue`
  + `expression`
  + `arg`
  + `modifiers`
+ `VNode`
+ `oldVnode`



##### 动态参数

例如`v-mydirective:[argument]="value"`的`augument` 参数时动态的。





### 渲染函数与JSX

暂未写。













































