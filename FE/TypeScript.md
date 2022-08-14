# TypeScript



## 何为 TypeScript？

[官方定义](https://www.typescriptlang.org)：添加了类型系统的`JavaScript` 。

个人理解：明确了变量类型的`JavaScript`，其定义类型的语法很像苹果的`Swift` 语言。



### 类型特点

#### 静态类型

​	`JavaScript` 是动态类型，在运行阶段才会报错

​	`TypeScript` 是静态类型，在编译阶段就会报错

#### 弱类型

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
- TypeScript 是一门**静态类型、弱类型**的语言。
- TypeScript 是**完全兼容** JavaScript 的，它不会修改 JavaScript 运行时的特性。
- TypeScript 可以编译为 JavaScript，然后运行在浏览器、Node.js 等任何能运行 JavaScript 的环境中。
- TypeScript 拥有很多编译选项，**类型检查**的严格程度由你决定。
- TypeScript 可以和 JavaScript **共存**，这意味着 JavaScript 项目能够渐进式的迁移到 TypeScript。
- TypeScript 增强了编辑器（IDE）的功能，提供了**代码补全、接口提示、跳转到定义、代码重构**等能力。
- TypeScript 拥有活跃的社区，大多数常用的**第三方库**都提供了**类型声明**。
- TypeScript 与标准同步发展，符合最新的 ECMAScript 标准（stage 3）。





## 基础

执行`TypeScript`文件：`tsc hello.ts`

```typescript
function sayHello(person: string) {
    return 'Hello, ' + person;
}

let user = 'Tom';
console.log(sayHello(user));
```



### 原始数据类型

数据类型分为两种：原始数据类型和对象类型。

原始数据类型包括：

+ 布尔值
+ 数值
+ 字符串
+ `null`
+ `undefined`
+ `Symbol` （ES10）
+ `BigInt`



### 任意值（Any）

​	如果是普通类型的值，在赋值后不允许改变类型，但是**任意值类型**可以。

##### 属性

​	在任意值上可以访问任何属性，也可以调用任何方法

⚠️若一个变量在声明时为指定类型，则默认被识别为任意值类型



##### 在数组中使用Any

若在将数组定义为`Any`类型，那么数组中可以出现任意类型。



### 联合类型

​		表示取值可以为多种类型中的一种

```typescript
let myFavoriteNumber: string | number;
myFavoriteNumber = 'seven';
myFavoriteNumber = 7;
```

#### 属性

​		由于有多种类型的可能，`TypeScript` 不能确定变量是哪个类型，因此只能访问联合类型里所有类型共有的属性或者方法

```typescript
function getString(something: string | number): string {
    return something.toString();
}
```

以上例子中，**something** 调用的方法必须是`string` 和`number` 类型共同有的。



### 接口

继承接口时，属性不能多，也不能少，必须完全一致。

#### 可选属性

可以在属性名后添加一个❓，这样该属性将变为**可选属性**。

简单示例：

```typescript
interface Person {
    name: string;
    age?: number;
}

let tom: Person = {
    name: 'Tom',
   // age: 25  //可写可不写
};
```



#### 任意属性

通过添加 `[propName:类型名]:类型名` 来定义任意属性。

`[]` 中的是键，前面一个类型名指键的类型名，后面一个类型名指值的类型名。

```typescript
interface Person {
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    name: 'Tom',
    gender: 'male'
};
```



#### 只读属性

只读属性只能在创建的时候被赋值。一旦创建完只能读取不能更改。

```typescript
interface Person {
    readonly id: number;
    name: string;
    age?: number;
    [propName: string]: any;
}

let tom: Person = {
    id: 89757,
    name: 'Tom',
    gender: 'male'
};
```





### 数组类型

最简单定义法：

```typescript
let fibonacci: number[] = [1, 1, 2, 3, 5];`
```



#### 数组泛型

使用**数组泛型**表示数组：

```typescript
let fibonacci: Array<number> = [1, 1, 2, 3, 5];
```



#### 接口

除非是表示类数组，一般不用这种方式。

```typescript
interface NumberArray {
    [index: number]: number;
}
let fibonacci: NumberArray = [1, 1, 2, 3, 5];
```



#### 类数组

```ts
function sum() {
    let args: number[] = arguments;
  
}
```

**arguments**其实是一个类数组，应该用接口表示

```tsx
function sum() {
    let args: {
        [index: number]: number;
        length: number;
        callee: Function;
    } = arguments;
}
```



### 函数

#### 声明

TS声明函数语法较为简单，与JS相差不大。

```typescript
function sum(x: number, y: number): number {
    return x + y;
}
```

#### 函数表达式

```typescript
//在TS中，=>符号不是箭头函数，而是表示函数定义，左边是输入类型，右边是输出类型
let sum:(x:number, y:number) => number = function(x:number, y:number): number {
  return x+y;
};
```

#### 接口应用

可以用接口来定义函数的形状，例如将**mySearch** 定义为`SearchFunc` 接口，这时

```typescript
interface SearchFunc {
    (source: string, subString: string): boolean;
}

let mySearch: SearchFunc;
mySearch = function(source: string, subString: string) {
    return source.search(subString) !== -1;
}
```



#### 参数

##### 		**可选参数**

​		函数中的参数和属性一样，可以设为**可选参数**，同样，也是只需要在参数名后面加一个`?`。

​	⚠️要注意的是，一旦出现了可选参数，后面就不能出现必须参数，也就是说，可选参数要放在参数列表的**最后**。

##### 		**参数默认值**

​		可以给参数添加默认值，添加了默认值后的参数默认被识别为**可选参数**，当然，加了默认值的参数**不用必须放在参数列表的最后面**

##### 		**剩余参数**

​		可以用**运算扩展符**（ES6）来获取函数中的剩余参数。

```tsx
function push(array: any[], ...items: any[]) {
    items.forEach(function(item) {
        array.push(item);
    });
}

let a = [];
push(a, 1, 2, 3);
```



#### 重载

​		重载允许一个函数接受不同数量或类型的参数，并作出相应的处理。



```ts
function reverse(x: number): number;
function reverse(x: string): string;
function reverse(x: number | string): number | string | void {
    if (typeof x === 'number') {
        return Number(x.toString().split('').reverse().join(''));
    } else if (typeof x === 'string') {
        return x.split('').reverse().join('');
    }
}
```



### 类型

#### 类型别名

用于给类型起一个新名字。

```tsx
type Name = string;   //用type使得Name成为string的别名
type NameResolver = () => string;
type NameOrResolver = Name | NameResolver;
function getName(n: NameOrResolver): Name { //这里Name指的就是string
    if (typeof n === 'string') {
        return n;
    } else {
        return n();
    }
}
```



#### 字符串字面量类型

​		用来约束取值只能是某几个字符串中的一个。

```tsx
type EventNames = 'click' | 'scroll' | 'mousemove';
function handleEvent(ele: Element, event: EventNames) {
    // do something
}

handleEvent(document.getElementById('hello'), 'scroll');  // 没问题
handleEvent(document.getElementById('world'), 'dblclick'); // 报错，event 不能为 'dblclick'
```



#### 类型断言

##### 语法

`值 as 类型`

##### 用途

1.将一个联合类型断言为其中一个类型

```tsx
interface Cat {
    name: string;
    run(): void;
}
interface Fish {
    name: string;
    swim(): void;
}

function isFish(animal: Cat | Fish) {
    if (typeof (animal as Fish).swim === 'function') {
        return true;
    }
    return false;
}
```



2.将一个父类断言为更加具体的子类

```tsx
class ApiError extends Error {
    code: number = 0;
}
class HttpError extends Error {
    statusCode: number = 200;
}

function isApiError(error: Error) {
  //将error类型断言为ApiError,否则Error类型没有code属性，会报错。
   if (typeof (error as ApiError).code === 'number'){	
    //if (error instanceof ApiError) { 
      //该处可以使用typeof，但是用instanceof更加合适，因为可以直接判断实例
      //但是如果ApiError为接口，那么不能使用instanceof
        return true;
    }
    return false;
}
```



3.将一个本来没有的属性添加到目标对象上

​		本来可能会报错，但是可以将对象类型**断言为Any**，这样就不会报错，因为Any属性可以访问任何属性。

​		⚠️除非非常确定该行为是正确的，否则不建议这么做。

```tsx
(window as any).foo = 1;//将window对象添加foo属性
```

##### 总结

​	只有`A` 与 `B` 存在兼容关系，两者才可互相断言。



### 常用对象

见[文档](https://ts.xcatliu.com/basics/built-in-objects.html) 

#### ES内置对象

+ Boolean
+ Error
+ Date
+ RegExp

#### DOM、BOM

+ Document
+ HTMLElement
+ Event
+ NodeList



## TS进阶

### 元组（Tuple）

#### 概念

​		数组内（不是Any类型）只能放相同类型的元素，但是元组可以放不同类型的元素。

#### 示例

```tsx
//定义一对值为string和number的元组
let tom:[string,number] = ['Tom', 25];

```

#### 越界元素

添加**越界元素**时，类型会被限制为元组中每个类型的联合类型。

```tsx
let tom: [string, number];
tom = ['Tom', 25];
tom.push('male');
tom.push(true); //true会被限制为string｜number类型
```



### 泛型

#### 概念

​		在定义函数、接口、类时，不预先定义类型，在使用的时候再指定类型。

#### 语法

```tsx
function createArray<T>(length: number, value: T): Array<T> {  //使用泛型，可以确保value与result类型相同，但又不用事先定好他们的类型，因为value的类型可能不确定。
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray<string>(3, 'x');
```



同样的，也可以为泛型定义多个类型参数，例如：

```typescript
function swap<T, U>(tuple: [T, U]): [U, T] {
    return [tuple[1], tuple[0]];
}

swap([7, 'seven']); // ['seven', 7]

```

#### 约束

​	如果参数是泛型，那么在函数内部不能随意操作它的属性/方法，因为你不知道它是什么类型，自然的，也就无法确定它有什么属性。

​	为了这个缺点，可以使用**泛型约束** ：

```typescript
interface Lengthwise {
    length: number;
}

function loggingIdentity<T extends Lengthwise>(arg: T): T {
    console.log(arg.length);
    return arg;
}
```

多个参数类型**互相约束**：

```typescript
function copyFields<T extends U, U>(target: T, source: U): T {
    for (let id in source) {
        target[id] = (<T>source)[id];
    }
    return target;
}

let x = { a: 1, b: 2, c: 3, d: 4 };

copyFields(x, { b: 10, d: 20 });
```

#### 泛型接口

```typescript
interface CreateArrayFunc<T> {
    (length: number, value: T): Array<T>;
}

let createArray: CreateArrayFunc<any>;
createArray = function<T>(length: number, value: T): Array<T> {
    let result: T[] = [];
    for (let i = 0; i < length; i++) {
        result[i] = value;
    }
    return result;
}

createArray(3, 'x'); // ['x', 'x', 'x']
```



#### 泛型类

与**泛型接口**相似：

```typescript
class GenericNumber<T> {
    zeroValue: T;
    add: (x: T, y: T) => T;
}

let myGenericNumber = new GenericNumber<number>();
myGenericNumber.zeroValue = 0;
myGenericNumber.add = function(x, y) { return x + y; };
```



### 声明合并

若出现了两个相同名字的函数、接口或者类，那么它们会合并成一个类型。







