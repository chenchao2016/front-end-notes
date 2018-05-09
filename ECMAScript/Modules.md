# 前端模块化

------

JavaScript模块化



## 一 、非模块化方式：

### 1、JavaScript内嵌



> - 缺乏代码的可重复性 ： A.html中的JavaScript不直接提供给B.html复用，如果存在重复使用的方法，需要粘贴复制
> - 缺乏依赖解析：A.html中c方法依赖a,b 方法，需要保证使用c之前添加了a.,b 
> - 全局命名空间污染：所有的函数和变量名称都将驻留在全局作用域



### 2、Script标签引入JavaScript文件



> - 缺乏依赖解析：高度依赖引入脚本的顺序。必须保证被依赖的a,b 方法在存在依赖的a方法之前加载
> - 代码维护性差：因为缺乏依赖解析，导致后续维护项目中无法直接辨别出js文件之间的关系
> - 全局命名空间污染：所有的函数和变量名称都将驻留在全局作用域



### 3、 使用模块对象和IIFE（Immediately-invoked function expression 立即执行函数表达式）

```javascript
//main.js
var app = {};
//b.js
(function(){
    app.add = function(){x,y}
    	return x+y;
})();
//c.js
(function(){
    app.output = function(){
        console.log('output!')
    }
})();

//a.js
(function(){
    app.add(1,2); // 3
    app.output(); // 'output!'
})()
```



> - 缺乏依赖解析：仍然依赖JavaScript脚本的加载顺序
> - 全局命名空间污染：虽然IIFE将变量和函数名都保留在函数的作用域内部，但是公开的对象 `app` 仍然暴露在全局变量下，全局变量的数量变成了 1 ，而不是 0



## 二、模块化方式

### 1、CommonJS —— CommonJS是服务器端模块的规范，由Node推广使用

​	2009年，有人讨论将 JavaScript 引入服务器端。因此 ServerJS 诞生了。随后，ServerJS 将其名称改为 CommonJS 。 

​	CommonJS 不是一个 JavaScript 库。它是一个标准化组织。它就像 ECMA 或 W3C 一样。ECMA 定义了 JavaScript 的语言规范。W3C定义了 JavaScript web API ，比如 DOM 或 DOM 事件。 **CommonJS 的目标是为 web 服务器、桌面和命令行应用程序定义一套通用的 API** 。

​	**CommonJS 还定义了模块 API** （**Nodejs实现了Commonjs风格的模块API**）。因为在服务器应用程序中没有 HTML 页面和 `</script><script>`标签，所以为模块提供一些清晰的 API 是很有意义的。模块需要被公开(`export`)以供其它模块使用，并且可以访问(`import`)。它的导出模块语法如下 ：

```javascript
//main.js
module.exports = function add(a, b){
  	return a+b;
}
```

导入的模块语法如下：

```javascript
var add = require('./main.js');
```



> 特点：
>
> - **CommonJs定义的模块同步加载**
> - **CommonJs定义的模块在服务端使用**



### 2、AMD —— Asynchronous Module Definition  异步模块定义  

由于CommonJs风格定义的模块方式的两个特点，所以CommonJs并不是浏览器端定义模块的最佳方式。为了把服务器端用的模块语法转换给浏览器使用，CommonJS 提出了几种模块格式，“Module/Transfer” 。其中之一，即 “Module/Transfer/C“，后来成为 [异步模块定义(AMD)](https://github.com/amdjs/amdjs-api/blob/master/AMD.md)  

定义方式：

```javascript
//入参是依赖项列表,并按照依赖表的数据顺序定义形参
define(['depsA','depsB'],function (a,b) {
  //code

   return {
      //code
   }
})
```



> CommonJS 和 AMD 解决了模块模式中剩下的两个问题：**依赖解析** 和 **全局作用域污染** 。我们只需要处理每个模块或每个文件的依赖关系就可以了。 并且不再有全局作用域污染 



**requireJs —— AMD 风格的模块定义** (**模块加载器**)

​	requireJs 是一个 JavaScript **模块加载器(module loader)** 。它可以根据需要异步加载模块。 下面是一个requirejs的基本用法：

```javascript
//main.js
require.config({
   baseUrl:'./',
   paths:{
      "jquery":"https://cdn.jsdelivr.net/npm/jquery@3/dist/jquery.min",
      "underscore":"https://cdn.jsdelivr.net/npm/underscore@1.9.0/underscore.min",
      "module":"require-module",  //非AMD标准定义的模块
      "sModule":"standard-module"
   },
   shim:{
      'module':{
         deps:[],
         exports:'myObject'  //导出myObject对象
      }
   }
})
//require所需要的依赖
require(['jquery','underscore','module','sModule'],function ($,_,module,standardM) {
 	//code
})

//require-module.js
var myObject = {
	hello: function () {
		console.log('hello world!')
	},
	_he: '1111'
}
//standard-module.js
define([''],function () {
	var add = function (x, y) {
		return x + y
	};

	return {
		add:add
	}
})
```



> 问题：
>
> - AMD的语法过于冗余（主要是所有的模块都需要使用define函数来定义）
> - 数组中的依赖列表必须与函数的参数列表匹配
> - 在当前浏览器下（HTTP 1.1），加载很多小文件会降低性能  （这个主要是请求次数问题吧？） —— 这一点可以使用打包工具解决（webpack、Browserify）



### 3、CMD —— Common Module Definition   通用模块定义 （国内发展出来的规范）

> **CMD 模块定义规范 :**https://github.com/seajs/seajs/issues/242



#### Sea.js —— 模块加载器

 : https://seajs.github.io/seajs/docs/

> **Seajs API 文档** ：http://yslove.net/seajs/





### 4、UMD  ——  Universal Module Definition  通用模块定义 

前面提到的几种模块定义方式—— 全局对象（Global Object）、CommonJs、AMD，可以有UMD的风格代码做一个兼容。其实 **UMD 是一套用来识别当前环境支持的模块风格的 if/else 语句。** 

```javascript
// UMD 风格编写的 sum 模块
//sum.umd.js
(function (root, factory) {
    if (typeof define === 'function' && define.amd) {
        // AMD
        define(['add', 'reduce'], factory);
    } else if (typeof exports === 'object') {
        // Node, CommonJS-like
        module.exports = factory(require('add'), require('reduce'));
    } else {
        // Browser globals (root is window)
        root.sum = factory(root.add, root.reduce);
    }
}(this, function (add, reduce) {
    //  private methods
 
    //    exposed public methods
    return function(arr) {
      return reduce(arr, add);
    }
}));
```



### 5、ES6模块语法

上面提到的各种模块定义方式（服务端——CommonJs、浏览器端——AMD、CMD、UMD、Global Object）都是为了**解决 JavaScript语言中没有内置模块系统的缺陷（ES6之前的ES版本）** ，但是在ES6语言的规范中，模块是JavaScript的一部分！

ES6使用import和export关键字来导入和导出模块。

```javascript
import module from 'es6-module'

//es6-module
const obj = {
	name:'ES6'
}
export obj
```

> - 目前ES6的模块语法暂时没有得到浏览器的普遍支持，因此，需要配合打包转换工具使用，以兼容低版本的浏览器，一般使用的解析器是babel



## 三、模块化工具

### 1、Browserify  

> **模块打包器(module bundler)**   

为了解决CommonJs定义的模块只能用户服务端、且同步加载的问题。通过Browserify可以让浏览器应用程序使用CommonJs模块。**Browserify通过遍历代码的依赖树，将依赖树中的所有代码打包成一个文件**

打包是否 支持 异步或者同步 ？



### 2、Webpack

> **模块打包器(module bundler)**   

跟Browserify一样，同样是一款模块打包器，可以遍历依赖树，然后将其打包到一个或者多个文件。但是Webpack可以处理CommonJs、AMD、ES6模块，并且，Webpack还有更多的灵活性和功能特性：

> - 代码分离
> - 加载器
> - 插件
> - Tree-shaking （类比RollUp）



### 3、Rollup 

> **模块打包器(module bundler)**   

​	Rollup 是另一个 JavaScript ES6 模块打包器。与 Browserify 和 Webpak 不同，rollup 只包含在项目中用到的代码。如果有大模块，带有很多函数，但是你只是用到少数几个，rollup 只会将需要的函数包含到打包文件中，从而显著减少打包文件大小。 

​	Rollup 普及了 JavaScript 圈内一个重要的特性：Tree shaking，即是指消除JavaScript上下文中无用代码，或更精确地说，只保留有用的代码。 



### 4、SystemJS

> **通用的模块加载器**

SystemJS 是一个通用的模块加载器，它能在浏览器或者 NodeJS 上动态加载模块，并且支持 CommonJS、AMD、全局模块对象和 ES6 模块。通过使用插件，它不仅可以加载 JavaScript，还可以加载 CoffeeScript 和 TypeScript。

SystemJS 的另一个优点是，它建立在 ES6 模块加载器之上，所以它的语法和 API 在将来很可能是语言的一部分，这会让我们的代码更不会过时。

### 5、JSPM

> **JSPM 既是包管理器，又是模块加载器，又是模块打包器** 



JSPM 是 JavaScript 开发工具的瑞士军刀，它是既是**包管理器(类似于 npm)，又是模块加载器（SystemJS），又是模块打包器(RollUp)**。

现代 JavaScript 开发很少只是需要自己的模块，绝大部分时候，我们还需要第三方模块。使用 JSPM，我们可以使用如下的命令，从 NPM 或者 Github 安装第三方模块：

```javascript
jspm install npm:package-name or github:package/name
```

上述命令会从 `npm` 或者 `github` 下载包，并将包安装到 `jspm_packages` 目录。

在开发模式下，我们可以使用 `jspm-server` 。像 Webpack Dev Server 一样，它会检测代码改变，重新加载浏览器来显示改变。与 Webpack Dev Server 不同的是，jspm-server 用的是 SystemJS 模块加载器。所以，每次它检测了文件的改变时，不会将所有文件读取来打包，而是只加载页面所需要的模块。

在部署时，肯定要打包代码。JSPM 带有打包器，可以用如下命令对代码打包：

```javascript
jspm bundle main.js bundle.js
```

在幕后，JSPM 用 rollup 作为它的打包器。 



## 四、模块化对比



**目前前端模块定义的方式由以下5种：CommonJs,AMD、CMD、UMD、ES6。**

 CommonJs用在**服务器端**，AMD和CMD用在**浏览器环境**，UMD可以通过检测当前环境，对服务器端、浏览器端进行代码兼容。



#### AMD与CMD的差异

 AMD 是 RequireJS 在推广过程中对模块定义的规范化产出。

 CMD 是 SeaJS 在推广过程中对模块定义的规范化产出。

类似的还有 CommonJS Modules/2.0 规范，是 BravoJS 在推广过程中对模块定义的规范化产出。 



 AMD:提前执行（异步加载：依赖先执行）+延迟执行

 CMD:延迟执行（运行到需加载，根据顺序执行）

 

####  RequireJS  和 SeaJS的异同：

相同点：

**RequireJS 和 Sea.js 都是模块加载器，倡导模块化开发理念，核心价值是让 JavaScript 的模块化开发变得简单自然。都是为了达成浏览器端模块开发为目的** 

差异：

1、对于依赖的模块，AMD 是**提前执行**，CMD 是**延迟执行**。不过 RequireJS 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。CMD 推崇 as lazy as possible. 

2、 https://github.com/seajs/seajs/issues/277

3、https://www.douban.com/note/283566440/

测试结果：

**因此我们得出结论(分别使用SeaJS 2.0.0和RequireJS 2.1.6进行测试)**

> **SeaJS只会在真正需要使用(依赖)模块时才执行该模块**
>
> SeaJS是异步加载模块的没错, 但执行模块的顺序也是严格按照模块在代码中出现(require)的顺序, 这样才更符合逻辑吧! 你说呢, RequireJS?
>
> 而RequireJS会先尽早地执行(依赖)模块, 相当于所有的require都被提前了, 而且模块执行的顺序也不一定100%就是按照require的顺序加载模块。

4、代码风格

```javascript
// CMD
define(function(require, exports, module) {   
    var a = require('./a')   
    a.doSomething()   
    // 此处略去 100 行   
    var b = require('./b')
    // 依赖可以就近书写   
    b.doSomething()   
    // ...
})
// AMD 默认推荐的是
define(['./a', './b'], function(a, b) { 
    // 依赖必须一开始就写好   
    a.doSomething()   
    // 此处略去 100 行   
    b.doSomething()    
        ...
})


参考知乎问答：
作者：玉伯
链接：https://www.zhihu.com/question/20351507/answer/14859415
```

虽然 AMD 也支持 CMD 的写法，同时还支持将 require 作为依赖项传递 

5、AMD 的 API 默认是**一个当多个用**，CMD 的 API 严格区分，推崇**职责单一**。 



#### 参考文档：

JavaScript模块化简史：<http://www.css88.com/archives/7628> 

前端模块化（CommonJs,AMD和CMD）： https://www.jianshu.com/p/d67bc79976e6



