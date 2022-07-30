### JS



### 类

#### apply



#### call







### This

#### 描述

##### 全局

在全局中this指向全局对象（window）

##### 函数

严格模式：未设置值时this为undefined

一般模式：默认为全局对象（window）

**<u>可以利用apply和call方法将this的值从一个环境传到另一个</u>**

##### 类

+ 构造函数中，是一个常规对象。所有非静态方法会加入到this的原型中

##### 派生类

没有初始的`this` 绑定，构造函数中调用`super()` 会生成 this 绑定。派生类不能在调用 `super()` 之前返回

+ 除非没有构造函数，或者构造函数返回一个对象

##### bind

this被绑定到bind的第一个参数中

否则若把方法从对象中拿出来，this默认指向全局对象

##### 箭头函数

this与封闭环境的this保持一致







### 标准库

#### Function

**function.name** 返回函数实例的名称

> 未声明的函数JavaScript将自行推断











### 严格模式

调用严格模式： `'use strict'`



#### 为函数开启严格模式

在函数体内部所有语句之前声明 `'use strict'`



#### 规范与特性

+ 一些原本不会报错的小错误会转为异常
+ 无法再意外创建全局变量
+ 引起静默失败（给NaN赋值会引起异常）
+ 试图删除不可删除的属性会抛出异常
+ 要求同一个函数的参数名唯一
+ 禁止八进制数字语法 
+ 禁止设置 `primitive` 值的属性


