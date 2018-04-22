# angular.module Function

​	用**angular.module**来**注册和检索模块**。所有模块应提供给一个应用程序必须使用这种机制注册。 当传递了2个或更多的参数时，创建一个新的模块。如果仅通过一个参数，一个现有的模块（作为第一个参数传递给模块）被检索。—— **多个参数时，创建模块，一个参数时，检索模块**。

​	module是angular中重要的模块组织方式，它提供了**将一组内聚的业务组件封装在一起的能力**。**这样做可以将代码按照业务领域问题分module的封装，然后利用module的依赖注入其关联的模块内容**，使得我们能够更好的”分离关注点“，达到更好的”高内聚低耦合“。

​	同时，module也是angular代码的入口，首先需要声明module，然后才能定义angular中的其他组件元素，例如 —— **config代码块、run代码块、controller、service、filter、directive等**

> `angular.module(name, [requires], [configFn]);`
>
> 参数：
>
> | 参数名称                | 参数类型   | 描述                                       |
> | ------------------- | ------ | ---------------------------------------- |
> | name                | string | 创建或检索模块的名称。                              |
> | requires (optional) | 数组     | 如果指定了新模块，则正在创建。如果未指定，则该模块将被检索以进一步配置。     |
> | configFn (optional) | 数组     | **模块的可选配置功能。** 在angular config阶段会调用该函数,例如配置应用程序的路由信息等，等同于 module.config 函数，可以用该函数替换使用。 |

> 返回值 ： **module对象**

注意：angular.module创建多个module模块，如果出现**重名**，则后者覆盖前者



- 特点

  >  模块定义了一个**应用程序**。

  > 模块是应用程序中不同部分的**容器**。

  > 模块是应用**控制器的容器**。

  > 控制器通常属于一个模块。

  ​


## module 的 config 、 run

**angularjs 执行启动阶段** ： **config ——> run** ——> compile/link

- config ： module模块加载阶段，应用在此时未启动
- run  ： angularjs应用第一个被运行的方法（类似于main入口函数）
  - module 的加载和依赖

> **module是配置块和运行块的集合，这些块都被用于引导进程中**
>
> - 配置块：在provider的注册和配置阶段执行；
>
> > module配置块的快捷配置方式 ：
> >
> > ```javascript
> > angular.module('moduleName',[])
> >        .value()
> >        .factory()
> >        .directive()
> >        .filter();
> >
> >    // 等价于  
> > angular.module('moduleName',[])
> >        .config(function ($provide, $compileProvider, $filterProvider) {
> >            $provide.value();
> >            $provide.factory();
> >            $compileProvider.directive();
> >            $filterProvider.register();
> >        })
> > ```
>
> - 运行块：在injector创建之后执行，被用于启动App；​

- config 、 run 运行顺序 —— config 优先于 run


```javascript
angular.module('appService',[])
    .service('serviceService',function (nowValue) {
        console.log('nowValue ,',nowValue)
    })
    .config(function () {
        console.log('Service module config :' ,'no time') 
    })
    .run(function (startTime) {
        console.log('Service module run :',startTime)
    });
var app = angular.module('app',['appService'])
    app.constant('startTime',new Date().toLocaleDateString())
        .value('nowValue',new Date().toLocaleDateString());

    app.config(function (startTime) {
     	console.log('Main module config :' ,startTime) 
    })
    .run(function (startTime) {
     	console.log('Main module run :' ,startTime)
    })
```

输出顺序：

```javascript
Service module config : no time
Main module config : 2018/4/22
Service module run : 2018/4/22
Main module run : 2018/4/22
```

- config 、 run 注入内容

> `config可以注入$stateProvider, $urlRouterProvider, $controllerProvider, $provide, $httpProvider等等provider`
> `run可以像controlle一样注入service,例如配置公共变量等`




## angular.module对外API



```javascript
var moduleInstance = {
    _invokeQueue: invokeQueue,
    _runBlocks: runBlocks,
    requires: requires,
    name: name,
    provider: invokeLater('$provide', 'provider'),
    factory: invokeLater('$provide', 'factory'),
    service: invokeLater('$provide', 'service'),
    value: invokeLater('$provide', 'value'),
    constant: invokeLater('$provide', 'constant', 'unshift'),
    animation: invokeLater('$animateProvider', 'register'),
    filter: invokeLater('$filterProvider', 'register'),
    controller: invokeLater('$controllerProvider', 'register'),
    directive: invokeLater('$compileProvider', 'directive'),
    config: config,
    run: function (block) {
        runBlocks.push(block);
        return this;
    }
};
if (configFn) {
    config(configFn);
}
return moduleInstance;
 
function invokeLater(provider, method, insertMethod) {
    return function () {
        invokeQueue[insertMethod || 'push']([provider, method, arguments]);
        return moduleInstance;
    };
}

```

​	从`moduleInstance`的定义，我们能够看出，`angular.module`为我们公开的API有：`invokeQueue、runBlocks、requires、name、provider、factory、service、value、constant、animation、filter、controller、directive、config、run`。其中invokeQueue和runBlocks是按名约定的私有属性，请不要随意使用，其他API都是我们常用的angular组件定义方法，从invokeLater代码中能看到**这类angular组件定义的返回依然是moduleInstance实例**，这就形成了流畅API，推荐使用**链式定义**这些组件，而不是声明一个全局的module变量。

​	

> | factory  | factory() 方法是创建和配置服务的最快捷方式,单例对象,在应用的生命周期内只会被调用一次注入factory，相当于注入factory定义时的函数调用入口。用 Factory 就是创建一个对象，为它添加属性，然后把这个对象返回出来。你把 service 传进 controller 之后，在 controller 里这个对象里的属性就可以通过 factory 使用了 |
> | -------- | ---------------------------------------- |
> | service  | service() 可以注册一个支持构造函数的服务，它允许我们为服务对象注册一个构造 函数注入service，相当于注入service定义时的function实例Service 是用"new"关键字实例化的。因此，你应该给"this"添加属性，然后 service 返回"this"。你把 service 传进 controller 之后，在controller里 "this" 上的属性就可以通过 service 来使用了。 |
> | provider | 只有provder是能传 .config() 函数的 service。**如果想在 service 对象启用之前，先进行模块范围的配置，那就应该选择 provider**。需要注意的是：在config函数里注入provider时，名字应该是：**providerName+Provider.** |
> | constant | Angular常量,相当于其他语言中的静态常量类                 |
> | value    | 类似constant,value()方法和constant()方法之间最主要的区别是，constant可以注入到配置函数config中，而value不行 |
>



- factory  ——  工厂模式 （var f = myFactory()； 返回一个对象）


```javascript
angular.module('service',[])
    .factory('serviceFactory',function () {
        var factory = new Object();
        factory.name = 'f-jay';
        factory.getName = function () {
            return factory.name;
        }
        factory.setName = function (newName) {
            factory.name = newName;
        }
        return factory;
    })
```

```javascript
var app = angular.module('app',['service']);
app.controller('ctrl',function ($scope,serviceFactory) {
    $scope.name = serviceFactory.getName();
 	serviceFactory.setName('jj');
    $scope.newName = serviceFactory.getName();
})
```

```html
<div ng-app="app" ng-controller="ctrl">
    <h2>{{name}}</h2>  // 输出 f-jay
    <h2>{{newName}}</h2> // 输出 jj
</div>
```


- service —— 构造函数模式 （var s = new myService() ； new 一个对象）


```javascript
angular.module('service',[])
    .service('serviceService',function () {
        this.name = 's-jay';
        this.getName =function () {
            return this.name;
        };
        this.setName = function (name) {
            this.name = name;
        }
    })
```

```javascript
var app = angular.module('app',['service']);
app.controller('ctrl',function ($scope,serviceService) {
    $scope.name = serviceService.getName();
 	serviceService.setName('jj');
    $scope.newName = serviceService.getName();
})
```

```html
<div ng-app="app" ng-controller="ctrl">
    <h2>{{name}}</h2>  //输出 s-jay
    <h2>{{newName}}</h2> //输出 jj
</div>
```




- provider —— 只有provder是能传给 .config() 函数的 service


```javascript
angular.module('service',[])
    .provider('serviceProvider',function () {
     var name = 'p-jay';
     getName = function () {
      return name;
     };
     setName = function (n) {
      name = n;
     };
     this.$get = function () {  //Provider 'serviceProvider' must define $get factory method.
            return {
               getName : getName,
               setName : setName
            }
     }
    })
```

> **this.$get 工厂函数式必填项！**

不使用config配置：

```javascript
var app = angular.module('app',['service'])
app.controller('ctrl',function ($scope,serviceProvider) {
    $scope.name = serviceProvider.getName();
 	serviceProvider.setName('jj');
    $scope.newName = serviceProvider.getName();
})
```

```html
<div ng-app="app" ng-controller="ctrl">
    <h2>{{name}}</h2> //输出 p-jay
    <h2>{{newName}}</h2>  //输出 jj
</div>
```

使用config配置块：

```javascript
var app = angular.module('app',['service']).config(function (serviceProviderProvider) { 
 serviceProviderProvider.$get().setName('p-chan')
});
app.controller('ctrl',function ($scope,serviceProvider) {
    $scope.name = serviceProvider.getName();
 	serviceProvider.setName('jj');
    $scope.newName = serviceProvider.getName();
})
```

>  如果 config函数中的入参 是 **serviceProvider**，这方法抛出异常 ： **Unknown provider: serviceProvider** ，表明入参的命名方式 为  `providerName + Provider`

```html
<div ng-app="app" ng-controller="ctrl">
    <h2>{{name}}</h2> //输出 p-chan
    <h2>{{newName}}</h2> // 输出 jj 
</div>
```

- constant 、 value 函数

  ```javascript
  angular.module('appService',[])
      .service('serviceService',function (nowValue) {
          console.log('nowValue ,',nowValue)
      })
      .config(function () {
          console.log('Service module config :' ,'no time') 
    //如果使用 nowValue ，则 Unknown provider: nowValue ; 如果使用 startTime ，则 Unknown provider: 	// startTime
      })
      .run(function (startTime) {
          console.log('Service module run :',startTime)
      });
  var app = angular.module('app',['appService'])
      app.constant('startTime',new Date().toLocaleDateString())
          .value('nowValue',new Date().toLocaleDateString());

      app.config(function (startTime) {
       console.log('Main module config :' ,startTime) //这里使用 nowValue 则 Unknown provider: nowValue
      })
      .run(function (startTime) {  //这里使用 nowValue 也是 没问题的
       console.log('Main module run :' ,startTime)
      })
  ```

> **angularjs设置全局变量：**
>
> ​	angularjs自身有二种，设置全局变量的方法，在加上js的设置全局变量的方法，总共有三种。要实现的功能是，在**ng-app中定义的全局变量，在不同的ng-controller里都可以使用。**
>
> 1，通过var 直接定义global variable，这根纯js是一样的。
>
> 2，用angularjs value来设置全局变量 。
>
> 3，用angularjs constant来设置全局变量 
>
> ```javascript
> var app = angular.module('app',[])
> //定义全局变量
> app.constant('name','Chen');
> app.value('age','10');
> var gender = 'boy';
> //在 controller中直接调用全局变量
> app.controller('ctrl',function ($scope,name,age) {
>     $scope.name = name
>     $scope.age = age
>     $scope.gender = gender
> })
> ```
>
> ```html
> <div ng-app="app" ng-controller="ctrl">
>     {{name}}  //输出 Chan
>     {{age}} // 输出 10
>     {{gender}} //输出 boy
> </div>
> ```




## angular.module的使用 (最佳实践)

在Angularjs中，模块是定义应用最主要的方式，模块包含了主要的应用代码。**一个应用可以包含多个模块，每个模块都包含了定义具体功能的代码**。

​	**在实际的项目中，最好不要只写一个大模块，而是应该分成不同的小模块**，如下：

> 每个功能一个module ；
>
> 每个可重用的组件作为一个module ，特别是 directive 和 filter ；
>
> 一个APP级别的module， 它依赖于上述模块 且 有一些初始化的配置；

**demo :**

```html
<div ng-app="moduleName"></div>
```

```javascript
angular.module('moduleName.service',[]).value().value();
angular.module('moduleName.directive',[]);
angular.module('moduleName.filter',[]);
angular.module('moduleName',['moduleName.service','moduleName.directive','moduleName.filter'])
       .config(function (injectables) { //provider注入
           //配置块 —— 仅可以注入provider，不能注入实例
       })
       .run(function (injectables) { // 实例注入
           //运行块 —— 仅可以注入实例，不能注入provider
       })
       .controller('moduleNameCtrl',function (args) {
           //控制器
       })
```





参考文档：

1. [angular学习（十四）——Module]: https://blog.csdn.net/lastsweetop/article/details/57417779	"注释"

2. [AngularJS angular.module]: http://www.angularjs.net.cn/api/71.html

3. [简话Angular]: https://www.cnblogs.com/meteorcn/p/4681114.html
