## 使用 jquery 加载代码

### 不适用 jquery

我们不适用 jquery 时导入文件的方式: 

```html
<head>
    <script>
        window.onload = function () {
            var oDiv = document.getElementById('app')
            oDiv.innerHTML = 'hello world'
        }
    </script>
</head>
<body>
    <div id="app"></div>
</body>
```



### 使用 jquery

> 将获取元素的语句写到页面头部，会因为元素还没有加载而出错，jquery提供了ready方法解决这个问题，它的速度比原生的 window.onload 更快。

```javascript
// 使用格式:

$(document).ready(function(){
	...
})
```

例如:

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(document).ready(function(){
            var $div = $('#app')
            $div.html('hello world')
        })
    </script>
</head>
<body>
    <div id="app"></div>
</body>
```



可以简写为：

```javascript
// 使用格式:

$(function(){
	...
})
```

例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('#app')
            $div.html('hello world')
        })
    </script>
</head>
<body>
    <div id="app"></div>
</body>
```



### 总结: 

- 使用 **jquery** 能够简化我们书写的代码
- jquery 的 ready 调用方法比我们之前接触的 window.onload 方法调用的要早:  ready 是html加载完毕之后就调用, onload是在资源文件也加载完毕之后才调用.
- jquery 的 ready 有简略写法, 我们往往使用它的简略写法.

