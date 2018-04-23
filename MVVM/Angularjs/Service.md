# Service

- $rootScope 

​    scope是angularJS中的作用域(其实就是**存储数据的地方**)，很类似javascript的原型链 。搜索的时候，优先找自己的scope，如果没有找到就沿着作用域链向上搜索，直至到达**根作用域rootScope**。

​	`$rootScope`是由**angularJS加载模块的时候自动创建的，每个模块(module)只会有1个rootScope**。rootScope创建好会以服务的形式加入到 `$injector`中。也就是说通过 `$injector.get("$ rootScope ")`;能够获取到某个模块的根作用域。更准确的来说，`$rootScope`是由angularJS的核心模块ng创建的。

​	**scope是html和单个controller之间的桥梁**，数据绑定就靠他了。**rootscope是各个controller中scope的桥梁**。用rootscope定义的值，可以在各个controller中使用。



- $state



- $stateParams