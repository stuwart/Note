## TypeScript



### 何为 TypeScript？

[官方定义](https://www.typescriptlang.org)：添加了类型系统的`JavaScript` 。

个人理解：明确了变量类型的`JavaScript`，其定义类型的语法很像苹果的`Swift` 语言。



#### 类型特点

##### 静态类型

​	`JavaScript` 是动态类型，在运行阶段才会报错

​	`TypeScript` 是静态类型，在编译阶段就会报错

##### 弱类型

​	是否允许**隐式类型转换**，可以分为**强类型** 和**弱类型**

`TypeScript` 与 `JavaScript` 都是弱类型，可以自动隐式类型转换。



**TypeScript**:

```typescript
console.log(1 + '1');
// 打印出字符串 '11'
```

**Python**:

```python
print(str(1) + '1')
```



#### 总结

**什么是 TypeScript？**

- TypeScript 是添加了类型系统的 JavaScript，适用于任何规模的项目。
- TypeScript 是一门静态类型、弱类型的语言。
- TypeScript 是完全兼容 JavaScript 的，它不会修改 JavaScript 运行时的特性。
- TypeScript 可以编译为 JavaScript，然后运行在浏览器、Node.js 等任何能运行 JavaScript 的环境中。
- TypeScript 拥有很多编译选项，类型检查的严格程度由你决定。
- TypeScript 可以和 JavaScript 共存，这意味着 JavaScript 项目能够渐进式的迁移到 TypeScript。
- TypeScript 增强了编辑器（IDE）的功能，提供了代码补全、接口提示、跳转到定义、代码重构等能力。
- TypeScript 拥有活跃的社区，大多数常用的第三方库都提供了类型声明。
- TypeScript 与标准同步发展，符合最新的 ECMAScript 标准（stage 3）。





### 入门TypeScript

执行`TypeScript`文件：`tsc hello.ts`

```typescript
function sayHello(person: string) {
    return 'Hello, ' + person;
}

let user = 'Tom';
console.log(sayHello(user));
```



### 基础

#### 原始数据类型

数据类型分为两种：原始数据类型和对象类型。

原始数据类型包括：

+ 布尔值
+ 数值
+ 字符串
+ `null`
+ `undefined`
+ `Symbol`
+ `BigInt`



#### 任意值（Any）

​	如果是普通类型的值，在赋值后不允许改变类型，但是**任意值类型**可以。

##### 属性

​	在任意值上可以访问任何属性，也可以调用任何方法

⚠️若一个变量在声明时为指定类型，则默认被识别为任意值类型





#### 联合类型

​		表示取值可以为多种类型中的一种

```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

##### 属性

​		由于有多种类型的可能，`TypeScript` 不能确定变量是哪个类型，因此只能访问联合类型里所有类型共有的属性或者方法

```typescript
function getString(something: string | number): string {
    return something.toString();
}
```



#### 接口

继承接口时，属性不能多，也不能少，必须完全一致。

可以在属性名后添加一个❓，这样该属性将变为**可选属性**。

简单示例：

```typescript
interface Person {
    name: string;
    ?age: number;
}

let tom: Person = {
    name: 'Tom',
    age: 25
};
```







































