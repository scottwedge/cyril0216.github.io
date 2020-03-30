##  jquery动画

> 通过 animate 方法可以设置元素21某属性值上的动画，可以设置一个或多个属性值，动画执行完成后会执行一个函数。

```
使用方法:


/*
    animate参数：
    参数一：要改变的样式属性值，写成字典的形式
    参数二：动画持续的时间，单位为毫秒，一般不写单位
    参数三：动画曲线，默认为‘swing’，缓冲运动，还可以设置为‘linear’，匀速运动
    参数四：动画回调函数，动画完成后执行的匿名函数
*/

$('#div1').animate({
	要改变的属性值
},动画持续时间,动画的效果,function(){
    动画执行完成之后调用的代码
});
```

例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <style>
        .box {
            width: 100px;
            height: 100px;
            background-color: pink;
        }
    </style>
    <script>
        $(function(){
            $('.btn').click(function(){
                $('.box').animate({
                    'width':'200px'
                },1000,'swing',function () {
                    $('.box').animate({
                        'height':'200px'
                    },1000,'swing',function(){

                    })
                })   
       
            })
        })
    </script>
</head>
<body>
    <div class="box"></div>
    <button class="btn">按钮</button>
</body>
```

最终效果: 

![QQ截图20180528141302](../images/QQ%E6%88%AA%E5%9B%BE20180528141302.png)

![QQ截图20180528141314](../images/QQ%E6%88%AA%E5%9B%BE20180528141314.png)

### 总结:

- 我们使用 animate( ) 方法来设置动画
- 动画内部有四个参数:  要更改的元素属性,  动画时长( 以毫秒计 ),    动画曲线,   动画完成之后所做操作
- 我们可以使用这样的方式来做一些简单动画.

