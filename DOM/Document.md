# HTML DOM Document 对象

**每个载入浏览器的 HTML 文档都会成为 Document 对象**。

Document 对象使我们可以从脚本中对 HTML 页面中的所有元素进行访问。

> ```javascript
> document === window.document //true
> ```
>
> 



1. ## cookie

读取cookie：

```javascript
document.cookie;
```



设置cookie：

```javascript
document.cookie = newCookie;
```

newCookie是一个**键值对形式的字符串**。需要注意的是，用这个方法一次只能对一个cookie进行设置或更新。

- 以下可选的cookie属性值可以跟在键值对后，用来具体化对cookie的设定/更新，使用分号以作分隔：

  - `;path=*path*` (例如 '/', '/mydir') 如果没有定义，默认为当前文档位置的路径。

  - `;domain=*domain*` (例如 'example.com'， '.example.com' (包括所有子域名), 'subdomain.example.com') 如果没有定义，默认为当前文档位置的路径的域名部分。

  - `;max-age=*max-age-in-seconds*` (例如一年为60 * 60 * 24 * 365)

  - `;expires=date-in-GMTString-format`

     如果没有定义，**cookie会在对话结束时过期**

    - 这个值的格式参见[Date.toUTCString()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/Date/toUTCString) 

  - `;secure` (cookie只通过https协议传输)

- cookie的值字符串可以用[encodeURIComponent()](https://developer.mozilla.org/en-US/docs/JavaScript/Reference/Global_Objects/encodeURIComponent)来保证它不包含任何**逗号、分号或空格(cookie值中禁止使用这些值).**