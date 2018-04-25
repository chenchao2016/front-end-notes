# Promise

处理JavaScript异步操作，是一种异步编程的解决方案

Promise内部有三种状态：**pending 、 fulfilled、rejected**

- pending : promise对象创建后的初始状态
- fulfilled   : 对象fulfill （resolve）时状态变为fulfilled
- rejected : 当对象reject（失败）时状态变为rejected

> 注意：对象状态只能从 `pending` 变为 `fulfilled` 或者 `rejected` 状态 ， 不能从 `fulfilled` 变为 `rejected` 或者 `rejected`·变为 `fulfilled` 



Promise方法：`then 、catch 、resolve 、reject 、all 、 race`

> **then、catch  属于实例方法，resolve 、reject 、all 、 race属于静态方法**



用法：

```javascript
new Promise(function (resolve, reject) {});
```



- **then**

```javascript 
then(onResolve ,onReject)
demo :
new Promise(function(resolve){resolve('resolved')}).then(function(d){
  console.log(d)  // 输出 ： resolved
},function(e){
  console.log(e)  
})

```

then()方法接受两个参数，且通常是成功和失败的回调函数（**也可以为非函数，如果是非函数，就执行链式调用中then中的成功或者失败的回调**），在promise状态改变时分别调用。

当**promise**状态为**fulfill**时，会把resolve(data)中的data值传给成功的回调函数，如果promise状态为reject时，就会把reject（error）中的error值传给失败的回调函数。（**难道不是先调用resolve/reject再改变状态？**）

promise构造函数中通常都是异步的，所以then方法往往都会优先于resolve和reject方法执行，所以promise内部需要有一个存储fulfill时调用函数的数组和存储reject时调函数的数组。

**then方法会返回一个新的 promise** **，并可以继续在改全新的promise上添加then方法，进行链式调用**

```javascript
var p = new Promise(function (resolve, reject) {
   resolve('resolved')
})
var p1 = p.then(function (d) {
   console.log('d', d);  //输出 ： d resolved
}, function (e) {
   console.log('e', e)
})

p == p1  //输出 ： false
```

**如果 p 的状态为fulfill** ，后面的then方法的链式调用(第二个then方法)的状态可以为fulfill，也可能是reject

1、状态为fulfill —— 含有fulfill状态的回调函数

```javascript
var p = new Promise(function (resolve, reject) {
		resolve('resolved')
	})
	var p1 = p.then(function (d) {
		console.log('d', d);  //  d resolved
	}, function (e) {
		console.log('e', e)
	}).then(function (fd) {
        console.log('fd',fd)  // fd undefined
	},function (fe) {
        console.log('fe',fe)
	})
```

2、状态为fulfill—— fulfill状态时，成功回调的入参为非函数（string/number/object等）

```javascript
var p = new Promise(function (resolve, reject) {
	resolve('resolved')
})
var p1 = p.then(undefined, function (e) {
	console.log('e', e)
}).then(function (fd) {
    console.log('fd',fd)   // fd resolved
},function (fe) {
    console.log('fe',fe)
})
```
3、状态为reject 

```javascript
var p = new Promise(function (resolve, reject) {
   resolve('resolved')
})
var p1 = p.then(function (d) {
   console.log('d', d);   //输出： d resolved
   throw new Error('reject!')   //或者使用Promise.reject('reject!')等
}, function (e) {
   console.log('e', e)
}).then(function (fd) {
   console.log('fd', fd)
}, function (fe) {
   console.log('fe', fe)  //输出： fe Error: reject!
    						// at <anonymous>:5:29
   							// at <anonymous>
})
```

**如果 p 的状态为reject** ，后面的then方法的链式调用(第二个then方法)的状态可以为reject，也可能是fulfill

1、状态为fulfill —— 含有reject状态的回调函数

```javascript
var p = new Promise(function (resolve, reject) {
   reject('rejected')
})
var p1 = p.then(function (d) {
   console.log('d', d);
}, function (e) {
   console.log('e', e) //e rejected
}).then(function (fd) {
   console.log('fd', fd) //fd undefined
}, function (fe) {
   console.log('fe', fe)
})
```

2、状态为reject —— reject状态时，成功回调的入参为非函数（string/number/object等）

```javascript
var p = new Promise(function (resolve, reject) {
   reject('rejected')
})
var p1 = p.then(function (d) {
   console.log('d', d);
},'no reject callback').then(function (fd) {
   console.log('fd', fd) 
}, function (fe) {
   console.log('fe', fe)  //fe rejected
})
```



> - 从 **p 的状态为fulfill** 的 第一个例子  和   **p 的状态为reject** 的 第一个例子对比 中可以看出**，第一个调用then方法返回的promise对象均为 resolve 状态的对象，不管 p 是否是resolve 或者是 reject 。**
> - 从 **p 的状态为fulfill** 的 第二个例子  和   **p 的状态为reject** 的 第二个例子对比 中可以看出，**如果第一个调用的then方法入参没有成功或者失败的回调函数（或者说是非函数类型），该方法返回的promise对象会将 p 的状态‘继承**’，在第二个then方法中执行成功或者失败的回调。



- **catch**

```javascript 
catch(onReject)
```

算是



> relose



- resolve



- reject



- all



- race



