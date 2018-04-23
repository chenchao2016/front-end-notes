# controller 控制器





定义方式：

1、直接使用function

```html
<div class="" ng-controller="personController">
    <h1>hello {{person.name}}</h1>
    <h1>{{person.sayHi()}}</h1>
</div>
```

```javascript
function personController($scope) {
    $scope.person = {};
    $scope.person.name = 'chen';
    $scope.person.sayHi = function () {
        console.log('hello everyone!');
    };
}
```

2、



## controller 与 controller之间 使用事件进行值传递 —— `$broadcast 、 $emit 、 $on`

使用事件的方式可以在不同的controller之间进行值传递。在一个controller中通过使用`$broadcast   ,  $emit`自定义事件，分别可以在父组件、子组件的controller中通过 $on 来获取自定义事件的信息。

用法：

```javascript
//自定义事件
$scope.$broadcast(name,arguments)  //传值给子组件
$scope.$emit(name,arguments)       //传值给父组件
//使用 $on 在监听事件
//接收两个入参——事件和数据
$scope.$on(eventName,function (event,data) {
	console.log(event , data)
});
```

demo :

```html
<div ng-controller="parentCtrl">
    <div ng-controller="selfCtrl">
        <button ng-click="doClick()">button</button>
        <div ng-controller="childCtrl"></div>
    </div>
    <div ng-controller="sibCtrl"></div>
</div>
```

```javascript
app.controller('selfCtrl',function ($scope) {
    $scope.doClick = function () {
        $scope.$broadcast('toChild','hi child!'); //传值给子组件 —— 广播
        $scope.$emit('toParent',' hi parent!');   //传值给父组件 —— emit
    }
})
app.controller('parentCtrl',function ($scope) {
   $scope.$on('toChild',function (event,data) {
      console.log(event , data)  //父组件未被触发，获取不到 selfCtrl 传来的值
    });
   $scope.$on('toParent',function (event,data) {
      console.log(event,data)    //父组件可以被触发，获取到 selfCtrl 传来的值
    })
})
app.controller('childCtrl',function ($scope) {
 $scope.$on('toChild',function (event,data) {
  	console.log(event , data)    //子组件可以被触发，获取到 selfCtrl 传来的值
 });
 $scope.$on('toParent',function (event,data) {
  	console.log(event,data)     //子组件未被触发，获取不到 selfCtrl 传来的值
 })
})
app.controller('sibCtrl',function ($scope) {
   $scope.$on('toChild',function (event,data) {
    	console.log(event , data)  //兄弟组件未被触发，获取不到 selfCtrl 传来的值
   });
   $scope.$on('toParent',function (event,data) {
    	console.log(event,data)   //兄弟组件未被触发，获取不到 selfCtrl 传来的值
   })
})
```

> **用** `$broadcast` **赋的值，只能子级得到值；**`$emit`**赋的值，只能父级得到；而同级的什么都不能得到。**



## controller 与 directive之间通信 

注意：controller与directive之间通信存在一定的耦合度，尽量避免使用

- directive 调用 controller方法 —— 总结的三种方式（推荐第三种做法）

```html
<div ng-controller="dCtrl">
    <my-directive message="message()">hello world!</my-directive>
    <my-directive2 fn="message()">hello world!</my-directive2>
</div>
```

```javascript
app.directive('myDirective',function () {
    return {
       restrict:"AE",
        transclude:true,
        template:'<h1><span ng-transclude=""></span></h1>',
        link:function ($scope,element,attrs ,ctrl) {
          element.bind('click',function () {
                $scope.message();  //方法一 ： 在同一个scope内，调用controller中的方法
                $scope.$apply(attrs.message) //方法二 ： 直接调用组件内属性的方法
      })
        }
    }
});

app.directive('myDirective2',function () {
    return {
       restrict:'AE',
        transclude:true,
        template:'<h1 ng-click="doClick()"><span ng-transclude=""></span></h1>',
        scope:{
          'fn':'&'
        },
        link:function (scope,element,attrs,ctrl) {
            scope.doClick = function () {
                scope.fn();   // 方法三 ： 使用  scope隔离作用域的方式调用
            }
        }
    }
})
```



- controller 调用 directive 方法 ？ 是否可行 ？ —— 待确定



参考文档：

1、[ angularjs `$broadcast $emit $on` 事件触发controller间的值传递 ] 	http://blog.51yip.com/jsjquery/1602.html