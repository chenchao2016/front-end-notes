# Event loop

关键字：JavaScript单线程、异步执行、同步执行、事件（任务）队列、执行队列

```javascript
setTimeout(function(){console.log(4)},0);
new Promise(function(resolve){
    console.log(1)
    for( var i=0 ; i<10000 ; i++ ){
        i==9999 && resolve()
    }
    console.log(2)
}).then(function(){
    console.log(5)
});
console.log(3);
//1、 2、 3 、 5 、4 
```

http://www.cnblogs.com/dong-xu/p/7000163.html#top