- ##  word-break : break-all 

> 英文或者数字超出横向宽度不会自动换行，需要使用该CSS属性



- ### ```<pre></pre>```标签的默认属性覆盖

> pre, xmp, plaintext, listing 的 user agent stylesheet  是：
>
> ```css
> pre, xmp, plaintext, listing {
>     display: block;
>     font-family: monospace;
>     white-space: pre;
>     margin: 1em 0px;
> }
> ```
>
> 但是有时候需要对这些标签内的内容进行换行显示，则，需要覆盖 ```white-space``` 属性。



