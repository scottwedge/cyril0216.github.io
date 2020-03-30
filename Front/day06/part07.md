## 绑定事件

> 给元素绑定click事件，可以用如下方法：



### 添加绑定事件

```javascript
使用方法:


$('#btn1').click(function(){

    // 内部的this指的是原生对象(标签)
    // 使用jquery对象用 $(this)  ==> [标签]

})
```



例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('.box')
            $div.click(function(){
                console.log('haha')

                console.log($(this))

                console.log(this)
            })
        })
    </script>
</head>
<body>
    <div class="box">哈哈</div>
</body>
```

点击之后的结果: 

![QQ截图20180528114507](../images/QQ%E6%88%AA%E5%9B%BE20180528114507.png)









## 获取元素的索引值

> 有时候需要获得匹配元素相对于其同胞元素的索引位置，此时可以用index()方法获取

```html
使用方法:

var $li = $('li').eq(1);

alert( $li.index() ); // 弹出1


<ul class="list">
    <li>1</li>
    <li>2</li>
    <li>4</li>
    <li>5</li>
    <li>6</li>
</ul>
```

例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function () {
            var $li = $('li')
            $li.click(function(){
                console.log( $(this).index() )
            })
        })
    </script>
</head>
<body>
    <ul>
        <li>11</li>
        <li>22</li>
        <li>33</li>
    </ul>
</body>
```

运行结果: 

![QQ截图20180528115712](../images/QQ%E6%88%AA%E5%9B%BE20180528115712.png)

## 课堂练习

> 选项卡的制作

![QQ截图20180528115919](../images/QQ%E6%88%AA%E5%9B%BE20180528115919.png)

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $btn = $('input')
            var $div = $('.tab_cons div')
            // console.log($btn)
            $btn.click(function(){
                var index = $(this).index()
                // console.log($btn[index])
                // 给当前点击元素添加样式
                $($btn[index]).addClass('active')
                // 给其他元素去除样式
                $($btn[index]).siblings().removeClass('active')                

                // 给当前点击元素添加样式
                $($div[index]).addClass('current')
                // 给其他元素去除样式
                $($div[index]).siblings().removeClass('current')
            })
        })
    </script>
</head>

<body>
    <div class="tab_con">
        <div class="tab_btns">
            <input type="button" value="按钮一" class="active">
            <input type="button" value="按钮二">
            <input type="button" value="按钮三">
        </div>
        <div class="tab_cons">
            <div class="current">按钮一对应的内容</div>
            <div>按钮二对应的内容</div>
            <div>按钮三对应的内容</div>
        </div>
    </div>
</body>
```



或者可以写成这样: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $btn = $('input')
            var $div = $('.tab_cons div')
            $btn.click(function(){
                // 当前元素添加, 其他元素去除
			   $(this).addClass('active').siblings().removeClass('active')
                
                $div.eq( $(this).index() ).addClass('current').siblings().removeClass('current')
            })
        })
    </script>
</head>

<body>
    <div class="tab_con">
        <div class="tab_btns">
            <input type="button" value="按钮一" class="active">
            <input type="button" value="按钮二">
            <input type="button" value="按钮三">
        </div>
        <div class="tab_cons">
            <div class="current">按钮一对应的内容</div>
            <div>按钮二对应的内容</div>
            <div>按钮三对应的内容</div>
        </div>
    </div>
</body>
```

### 总结:

- 我们想要给当前元素添加事件, 只需要选中当前元素, 这里调用 click 即可, 和原生 js 不同的是: 不用'on'开头
- 在点击事件之中,$(this) 就代表当前选中元素(jquery对象),  想要获取当前元素的索引值,可以使用 index( )方法
- 如果当前元素是一个dom对象, 变成 jquery 对象的方法是外边加`$`符号:  $(dom元素)
- 调用 jquery对象的下标,获取的是 dom元素对象:  $div[0]  ===>  dom元素对象



