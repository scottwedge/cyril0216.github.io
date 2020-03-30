# jquery样式操作



## 操作样式

```javascript
使用方法: 

// 获取div的样式
$("div").css("width");
$("div").css("color");

// 设置div的样式
$("div").css("width","30px");
$("div").css("height","30px");

// 可以使用引号引起来,也可以不使用,但是不使用时必须写成小驼峰命名的形式:  
// 例如:
$("div").css({'font-size':"30px",'color':'red'})
$("div").css({fontSize:"30px",color:"red"});
```



例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('div')
            $div.css('width','200px')
            $div.css('height','200px')
            // 需要注意: 多个属性同时设置,需要使用 {} 包裹
            $div.css({'background-color':'pink','color':'red'})
            $div.css({fontWeight:'bold',fontSize:'30px'})
            
            var width = $div.css('width')
            var height = $div.css('height')
            console.log(width)
            console.log(height)
        })
    </script>
</head>
<body>
    <div>这里有个div</div>
</body>
```

效果: 

![QQ截图20180528104516](../images/QQ%E6%88%AA%E5%9B%BE20180528104516.png)

```
200px
200px
```



**特别注意**

> 如果使用选择器获取了多个元素，那么, 获取的信息是第一个元素的，比如：$("li").css("width")，获取的是第一个 li 的 width。





## 操作额外样式

```
使用方法: 

$("#div1").addClass("divClass2") 				//	给id为div1的对象追加样式divClass2
$("#div1").removeClass("divClass")  			//	移除div1的divClass的样式类名
$("#div1").removeClass("divClass divClass2") 	 //	 移除多个样式
$("#div1").toggleClass("anotherClass") 			//	重复切换anotherClass样式
```

例如:

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('#div1')
            $div.addClass('divClass2')
            // $div[0] 可以将jquery对象变为元素对象
            console.log($div[0])
        })
    </script>
</head>
<body>
    <div id="div1" class="divClass">hahha</div>
</body>
```

效果: 

```
<div id="div1" class="divClass divClass2">hahha</div>
```



再例如:

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('#div1')
            $div.removeClass('divClass')
            console.log($div[0])
        })
    </script>
</head>
<body>
    <div id="div1" class="divClass">hahha</div>
</body>
```

结果: 

```
<div id="div1" class="">hahha</div>
```



或者例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('#div1')
            $div.addClass('divClass2')
            $div.removeClass('divClass divClass2')
            console.log($div[0])
        })
    </script>
</head>
<body>
    <div id="div1" class="divClass">hahha</div>
</body>
```

结果:

```
<div id="div1" class="">hahha</div>
```



下一个例如:

```html
<head>
    <script src="./jquery-1.12.4.min.js">
    </script>
    <style type="text/css">
        .c1 {
            color: red;
        }
    </style>
</head>

<body>
    <div id="div" class="c1">hello world</div>
    <button id="button"> click me</button>
    
    
    <script type="text/javascript">
        $('#button').click(function(){
            // 点击一个添加当前c1样式,再点击一次取消当前c1样式
            $("#div").toggleClass('c1');
        })
	</script>
</body>
```

效果: 

![QQ截图20180528113307](../images/QQ%E6%88%AA%E5%9B%BE20180528113307.png)



![QQ截图20180528113314](../images/QQ%E6%88%AA%E5%9B%BE20180528113314.png)



## 总结: 

- 使用 jquery 获取当前的标签, 得到标签之后,如果要对标签进行CSS样式的修改,直接调用 .css( ) 方法即可.
- .css( ) 方法, 可以当做 读取css样式值 来使用, 也可以当做 设置css样式值来使用
- 额外操作样式的方法就三个:  addClass( )    removeClass( )    toggleClass( )