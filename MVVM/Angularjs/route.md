# Route

—— 在web开发中，“route”是指根据 url 分配到对应的处理程序。

- ngRoute —— 路由模块
- uiRouter —— 第三方路由组件


1、ngRoute 的使用

```html
<ul>
    <li><a href="#/a/123">a</a></li>
    <li><a href="#/b">b</a></li>
</ul>
<div ng-view></div>
```

```javascript
var app = angular.module('app',['ngRoute'])
    .config(['$routeProvider',function ($routeProvider) {
        $routeProvider
            .when('/',{
               template:'<p>this is home!</p>'   
            })
            .when('/a/:index',{			
               templateUrl:'tpls/a.html',
                controller:'aCtrl'
            })
            .when('/b',{
               template:'<h1>this is b route! say {{greeting}}</h1>',
             controller:'bCtrl'
            })
            .otherwise({
                redirectTo:'/'
            })
    }])
app.controller('aCtrl',function ($scope,$routeParams,listService) {
   console.log($routeParams)  //{ index : "123"} 返回URL设置的对象
    $scope.list = listService.data()
})
app.controller('bCtrl',function ($scope) {
    $scope.greeting = 'hello world!'
})
app.service('listService',function () {
    this.data = function () {
        return [{
           key:'1',
            value:'111'
        },{
         key:'2',
         value:'222'
        },{
         key:'3',
         value:'333'
        }]
    }
})
```

`tpls/a.html` 路径下的模板页面：

```html
<ul>
    <li ng-repeat="item in list"><span>{{item.key}} : </span><span>{{item.value}}</span></li>
</ul>
```



$routeProvider.when 配置对象说明：

1. controller  ：//function或string类型。在当前模板上执行的controller函数，生成新的scope  
2. controllerAs ： //string类型，为controller指定别名  
3. template  ：//string或function类型，视图所用的模板，这部分内容将被ngView引用  
4. templateUrl  ：//string或function类型，当视图模板为单独的html文件或是使用了`<script type="text/ng-template">`定义模板时使用  
5. resolve ：// `object <key,function>`  **指定当前controller所依赖的其他模块**  
6. redirectTo ：//重定向的地址



2、UIRouter的使用





参考文档：

1、UI-Router for AngularJS (1.x) - Hello World!	https://ui-router.github.io/ng1/tutorial/helloworld



