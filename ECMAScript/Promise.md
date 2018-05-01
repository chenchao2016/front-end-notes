# Promise

处理JavaScript异步操作，是一种**异步编程的解决方案**

Promise内部有三种状态：**pending （进行中）、 fulfilled（已成功）、rejected（已失败）**

- pending : promise对象创建后的初始状态
- fulfilled   : 对象fulfill （resolve）时状态变为fulfilled
- rejected : 当对象reject（失败）时状态变为rejected

> 注意：
>
> - 对象状态只能从 `pending` 变为 `fulfilled` 或者 `rejected` 状态 ， 不能从 `fulfilled` 变为 `rejected` 或者 `rejected`·变为 `fulfilled` ，一旦状态改变，就不会再变
> - `Promise`对象的状态改变，只有两种可能：从`pending`变为`fulfilled`和从`pending`变为`rejected`。**只要这两种情况发生，状态就凝固了，不会再变了，会一直保持这个结果，这时就称为 resolved（已定型）**—— 阮一峰ES6教程



Promise方法：`then 、catch 、resolve 、reject 、all 、 race`

> **then、catch  、 finally 属于实例方法（Promise.prototype.then()、Promise.prototype.catch()、Promise.prototype.finally()），resolve 、reject 、all 、 race属于静态方法**



用法：

```javascript
new Promise(function (resolve, reject) {
      if (/* 异步操作成功 */){
      resolve(value);
    } else {
      reject(error);
    }
});
```

![promise-then](../files/imgs/ECMAScript/promise-then.png)

​						(转引自  廖雪峰的官方网站—— Promise 部分)

> Promise 构造函数接受的参数为一个函数，该函数接受的两个参数 **resolve 和 reject  ，这两个参数也是函数**，由 JavaScript 引擎提供，不用自己部署。
>
> - resolve 函数 —— 将 Promise对象的状态从 pending 变为 fulfilled ，并**将异步操作的结果作为参数传递出去**；
> - reject  函数 —— 将Promise对象的状态从pending 变为 rejected ，并**将异步操作报错的信息作为参数传递出去；**
>
>



- ## **then**

```javascript 
then(onResolve ,onReject)

demo :
new Promise(function(resolve){resolve('resolved')}).then(function(d){
  console.log(d)  // 输出 ： resolved
},function(e){
  console.log(e)  
})

```

then()方法接受两个参数，且通常是成功和失败的回调函数（**也可以为非函数，如果是非函数，就执行链式调用中then中的成功或者失败的回调**），在Promise状态改变时分别调用。**这两个回调函数都接受Promise对象传出的值作为参数。**   （回调函数的入参除了可以使用Promise传出的值作为参数，还可以使用 **return**  给回调函数传参数 ）

```javascript
Promise.resolve('ok').then(function (r) {
    return r;
}).then(function (r) {
    console.log('r : ', r) // r : ok  ,如果没有return r ，则输出 r : undefined
})
```



第一个回调函数是`Promise`对象的状态变为`resolved`时调用，并把resolve(data)中的data值传给该函数；第二个回调函数是`Promise`对象的状态变为`rejected`时调用，并把reject（error）中的error值传给该函数。

**Promise构造函数中通常都是异步的**，所以then方法往往都会优先于resolve和reject方法执行，所以promise内部需要有一个存储fulfill时调用函数的数组和存储reject时调函数的数组。( //TODO ??? )



```javascript
var p = new Promise(function (resolve, reject) {
	console.log('Promise')
	resolve('resolved')
})
var p1 = p.then(function (d) {
	console.log('d', d);
},'no reject callback')

console.log('console.log')

//执行结果
// Promise
// console.log
// d resolved
```
**then方法会返回一个新的 Promise**，并可以继续在改全新的Promise上添加then方法，**进行链式调用**

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
> - 从 **p 的状态为fulfill** 的 第二个例子  和   **p 的状态为reject** 的 第二个例子对比 中可以看出，**如果第一个调用的then方法入参没有成功或者失败的回调函数（或者说是非函数类型），该方法返回的promise对象会将 p 的状态和值‘继承’下去**，在第二个then方法中执行成功或者失败的回调。



- ## **catch**

```javascript 
catch(onReject)
```

该实例方法用于指定错误时的回调函数，是 then(null , onReject)的别名（It behaves the same as calling Promise.prototype.then(undefined, onRejected). ）。

​	所以说，catch方法只是then的一个语法糖。

> 知乎问题： [ES6 Promise Catch后面的then还是会执行？](https://www.zhihu.com/question/48765053/answer/112886955 )
>
> ```javascript
> function test(res) {
>     return Promise.resolve(res).then(res = > {
>         console.log(res += '!')
>         return res
>     })
>         .then(res = > {
>         console.log(res += '!')
>         return Promise.reject("end"); //此处返回一个新的promise;
>     })
>         .
>     catch (res = > {
>         console.log(res);
>         return res;
>     })
>         .then(res = > {
>         //为什么此处还是会执行！！
>         console.log(res += '!')
>     })
> }
> test('hello')
>
> //"hello!"
> //"hello!!"
> //"end"
> //"end!"
> ```
>
> 答：catch段执行完后，原有的promise 就结束了，而**返回了一个新fulfilled状态的Promise** 。 
>
> 备注：1. 先看[Promise.prototype.catch()](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/catch)
> It behaves the same as calling Promise.prototype.then(undefined, onRejected). 
> 这里说，catch()是then()的语法糖。
>
> 2. 再来看[Promise.prototype.then()](https://link.zhihu.com/?target=https%3A//developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/then)
>
> The then() method returns a Promise. 
> 这里说，then()返回的是一个promise。
>
> As the then and Promise.prototype.catch() methods return promises, they can be chained — an operation called composition.
> 这里说，因为then()和catch()又返回了一个promise，因此，后续调用可以串联起来。
>
> **Values returned from the onFulfilled or onRejected callback functions will be automatically wrapped in a resolved promise.**
> **then()的两个参数函数中返回的值，会自动包装成一个已resolved的promise。**



- ## finally

  **Promise.prototype.finally()**方法返回一个**Promise**，在执行**then()**和**catch()**后，都会执行**finally**指定的回调函数。避免同样的语句需要在**then()**和**catch()**中各写一次的情况。

  ```javascript
  p.finally(onFinally); //onFinally 为Promise 状态改变后的回调函数
  ```

  备注：该方法在ES6标准中并未实现

  **polyfill**

  ```javascript
  Promise.prototype.finally = function (callback) {
    let P = this.constructor;
    return this.then(
      value  => P.resolve(callback()).then(() => value),
      reason => P.resolve(callback()).then(() => { throw reason })
    );
  };
  ```

  (转引自   [阮一峰ES6教程 ——Promise部分](http://es6.ruanyifeng.com/#docs/promise#Promise-prototype-finally))

  ​

- ## resolve

  **Promise.resolve()** 它相当于创建了一个立即`resolve`的对象。

  ```javascript
  Promise.resolve('resolve').then(function (r) {
      console.log(r) ; // resolve
  })
  //等价于
  new Promise(function (resolve) {
      resolve('resolve')
  }).then(function (r) {
      console.log(r);  // resolve
  })
  ```

  ​

- ## reject

  同理，**Promise.reject()** 它相当于创建了一个立即`reject`的对象。示例代码同 `resolve`

  ​

- ## all

  **Promise.all(`iterable`)** 它接收一个promise对象组成的数组 **iterable** 作为参数，并返回一个新的`promise`对象实例。当此实例内的 promise 都是 fulfill 状态或者参数 中不包含promise时，回调完成（resolve）。如果参数中 promise 有一个 rejected ，则此实例回调失败（reject） , **失败的原因是 第一个 promise失败的原因**

  ```javascript
  var promise1 = Promise.resolve(3);
  var promise2 = 42;
  var promise3 = new Promise(function(resolve, reject) {
    setTimeout(resolve, 1000, 'foo');
  });

  Promise.all([promise1, promise2, promise3]).then(function(values) {
    console.log(values);  //大约1秒钟后输出 :  [2,42,'foo']
  });
  ```

  > 如果  `iterable` 其中有非promise对象实例，则直接直接传入到resolve的数组中
  >
  > ```javascript
  > Promise.all([1, false, 'all', undefined, null, {}, function () {},
  >     Promise.resolve('ok')]).then(function (values) {
  >     console.log(values) // [1, false, "all", undefined, null, {}, ƒ (), "ok"]
  > })
  > ```
  >
  > ​



- ## race

  **Promise.race(iterable) **方法返回一个 **promise** 对象实例， 只要 `iterable` 中有一个 （最早改变状态的那个）promise 对象状态发生改变——"成功(fulfilled)"或"失败(rejected)"，那么，该promise对象将会返回resolve的返回值或reject的错误原因。

  ```javascript
  Promise.race([Promise.resolve('ok'), false, Promise.reject('error')]).then(function (r) {
      console.log(r); // ok
  }).
  catch (function (e) {
      console.log(e)
  })
  ```

  ```javascript
  Promise.race([Promise.reject('error'), Promise.resolve('ok')]).then(function (r) {
      console.log(r);
  }).
  catch (function (e) {
      console.log(e)  //error
  })
  ```

  > 同理，如果 `iterable`  传入的是非promise对象，直接resolve。


