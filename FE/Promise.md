## Promise

### 何为Promise

**Promise**是ES6的新特性。

​	传统的使用回调函数的方式：

```javascript
getAsync("fileA.txt", function(error, result){
    if(error){// 取得失败时的处理
        throw error;
    }
    // 取得成功时的处理
});
//Node规定在回调函数中的第一个参数为`error` ，第二个参数为`result` .
```



使用**`Promise`** 后：

```javascript
var promise = getAsyncPromise("fileA.txt"); 
promise.then(function(result){
    // 获取文件内容成功时的处理
}).catch(function(error){
    // 获取文件内容失败时的处理
});
```



#### 简介

​	**Promise** 主要有三种API：

+ **Constructor**

  使用构造函数`Promise()`来创建一个新的对象

  ```javascript
  var a = new Promise(function(resolve,reject){
    
  });
  ```

  

+ **Instance Method**

  创建对象时，为了设置成功/失败时调用的回调函数，使用`then`方法`a.then(onFullfilled,onRehected)`

  + **resolve**(成功)时

    `onFullfilled`会被调用

  + **reject** （失败）时

    `onRejected`会被调用

  + 只想处理异常情况，可以使用`catch`

    `promise.carch(onRejected)`

+ **Static Method**

  `Promise` 作为全局对象，具有的静态方法

  + **promise.all**
  + **promise.resolve**



#### 编写代码

##### 示例

```javascript
function getURL(URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', URL, true);
        req.onload = function () {
            if (req.status === 200) {  //状态码为200时调用resolve函数，并且将res.responseText作为参数传入
                resolve(req.responseText);
            } else {
              //发生错误时调用reject函数，在其中创建了一个Error对象，并且将错误信息作为参数传入
                reject(new Error(req.statusText));
            }
        };
        req.onerror = function () {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}
// 运行示例
var URL = "http://httpbin.org/get";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
    console.error(error);
});
```



##### 处理方法

实例方法有两种：成功后调用的方法，失败后调用的方法。

![image.png](https://s2.loli.net/2022/08/12/5sGb3XPAQ1aeyCg.png)

**onFullfilled**:在`Promise`中 调用`resolve` 后，将**Promise** 对象变为**resolve**状态，同时用它的值调用该函数。

**onRejected**:发生错误后，调用`reject`，可以在`.then` 第二个参数或者`.catch` 方法中设置想要调用的函数





### Promise实战

#### 创建Promise对象

1. `new Promise()` 创建一个**promise** 对象
2. 在`fn` 中指定异步处理
   1. 处理结果正常则调用`resolve(处理结果值)`
   2. 处理结果失败则调用`reject(Error对象)`



接下来，我们来通过异步处理的方式获取XHR的数据。

#### 创建XHR的Promise对象

```js
function getURL(URL) {
    return new Promise(function (resolve, reject) {
        var req = new XMLHttpRequest();
        req.open('GET', URL, true);
        req.onload = function () {
            if (req.status === 200) {
                resolve(req.responseText); 
            } else {
                reject(new Error(req.statusText));
            }
        };
        req.onerror = function () {
            reject(new Error(req.statusText));
        };
        req.send();
    });
}
// 运行示例
var URL = "http://httpbin.org/get";
getURL(URL).then(function onFulfilled(value){
    console.log(value);
}).catch(function onRejected(error){
    console.error(error);
});
```



#### 创建处理方法

刚才创建的返回`Promise` 的函数：

`getURL("http://example.com/");`

该函数在执行`resolve(req.responseText)`时，将**peomise**变为`resolve(Fulfilled)`状态，同时调用`onFulfilled`函数。

同样的，发生错误的话，**promise**变为`Rejected`状态，同时调用`.then`后的第二个参数或者是在`.catch` 方法中设置想要调用的函数。















































