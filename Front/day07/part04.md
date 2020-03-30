# jquery事件

## 事件函数列表：

> 比原生 js  省略了 'on'
>
> 这里涉及到一些事件冒泡的内容, 主要原理:
>
> 我们点击子元素的时候,子元素会将事件传递到父元素去,进而触发父元素的事件.

```javascript
blur() 				//元素失去焦点
focus() 			//元素获得焦点
click() 			//鼠标单击
mouseover() 		//鼠标进入（进入子元素也触发）
mouseout() 			//鼠标离开（离开子元素也触发）
hover() 			//同时为mouseenter和mouseleave事件指定处理函数
ready() 			//DOM加载完成
submit() 			//用户递交表单
```



例如: 

```html
<head>
    <style>
        .box {
            width: 200px;
            height: 200px;
            background-color: pink;
        }
        .son {
            width: 100px;
            height: 100px;
            background-color: red;
        }
    </style>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $input = $('#input1')
            // 默认,一上来就选中
            $input.focus()
            // 取消焦点时调用的方法
            $input.blur(function(){
                // alert('haha')
            })

            var $div1 = $('.box')
            // mouseover和mouseout的使用
            // $div1.mouseover(function(){
            //     $div1.stop().animate({'margin-top':'60px'})
            // })
            // $div1.mouseout(function(){
            //     $div1.stop().animate({'margin-top':'0px'})
            // })

            // mouseenter和mouseleave的使用
            // $div1.mouseenter(function(){
            //     $div1.animate({'margin-top':'100px'})
            // })
            // $div1.mouseleave(function(){
            //     $div1.animate({'margin-top':'0px'})
            // })

            // hover的使用
            $div1.hover(function(){
                $div1.animate({'margin-top':'100px'})
            },function(){
                $div1.animate({'margin':'0px'})
            })
        })
    </script>
</head>
<body>
    <input type="text" id="input1">
    <div class="box">
        <div class="son"></div>
    </div>
</body>
```



再例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $form = $('#form1')
            var $user = $('#name')
            $form.submit(function(){
                alert($user.val())
                // 阻止提交事件
                return false
            })
        })
    </script>
</head>
<body>
    <form action="" id="form1">
        <p>
            <label for="name">姓名:</label>
            <input type="text" id="name"> 
        </p>
       
        <p>
            <label for="pswd">密码:</label>
            <input type="password" id="pswd">
        </p>
      
        <input type="submit">
    </form>
</body>
```



