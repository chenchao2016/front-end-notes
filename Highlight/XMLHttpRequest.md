# AJAX



[ajax](http://caibaojian.com/t/ajax)：一种请求数据的方式，不需要刷新整个页面；

ajax的技术核心是 **XMLHttpRequest 对象**；

ajax 请求过程：创建 **XMLHttpRequest** 对象、连接服务器、发送请求、接收响应数据；





### ActiveXObject

此对象为 Microsoft 扩展，仅在 Internet Explorer 中受支持 



### XDomainRequest

IE8/IE9 时代使用的以支持CORS功能的方法，从IE10开始可以直接使用更加通用的 XMLHttpRequest。

> **XDomainRequest is the only way of having an XHR that supports [CORS](http://en.wikipedia.org/wiki/Cross-origin_resource_sharing) in IE8 and 9.** At the time of IE8, Microsoft decided to come up with their own CORS XHR instead of the standard CORS XMLHttpRequest which is now used in IE10. Since **IE10, XDomainRequest has been removed**  
>
> **You should only use XDomainRequest if you need CORS in IE8/9.** XDomainRequest is not completely interchangeable（通用） with XMLHttpRequest, the interfaces aren't exactly the same. One is example is it doesn't support the `onreadystatechange` event. So if you want to switch between them like in the question, you will need to make sure you use `onload` not `onreadystatechange` and check any other functionality is interchangeable. 



### XMLHttpRequest



```javascript
if(window.XDomainRequest)
{
    this.ajaxRequest = new window.XDomainRequest();
}
else if (window.XMLHttpRequest)
{
    this.ajaxRequest = new window.XMLHttpRequest();
}
else
{
    this.ajaxRequest = new window.ActiveXObject('Microsoft.XMLHTTP');
}
```



jQuery ajax 设置超时时间：

```javascript
var ajaxTimeoutTest = $.ajax({
　　url:'',  //请求的URL
　　timeout : 1000, //超时时间设置，单位毫秒
　　type : 'get',  //请求方式，get或post
　　data :{},  //请求所传参数，json格式
　　dataType:'json',//返回的数据格式
　　success:function(data){ //请求成功的回调函数
　　　　alert("成功");
　　},
　　complete : function(XMLHttpRequest,status){ //请求完成后最终执行参数
　　　　if(status==='timeout'){//超时,status还有success,error等值的情况
 　　　　　 ajaxTimeoutTest.abort();
　　　　　  alert("超时");
　　　　}
　　}
});


```

设置timeout的时间，通过检测complete时status的值判断请求是否超时，如果超时执行响应的操作。

> 如果是同步请求，超时时间不起作用



参考文档：

1、[XDomainRequest vs XMLHTTPRequest](https://stackoverflow.com/questions/25141650/xdomainrequest-vs-xmlhttprequest)

2、[ActiveXObject 对象 (JavaScript)](https://msdn.microsoft.com/zh-cn/library/7sw4ddf8(v=vs.94).aspx)

