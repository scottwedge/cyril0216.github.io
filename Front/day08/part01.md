# 事件冒泡

## 什么是事件冒泡 

> 在一个对象上触发某类事件（比如单击onclick事件），如果此对象定义了此事件的处理程序，那么此事件就会调用这个处理程序，如果没有定义此事件处理程序或者事件返回true，那么这个事件会向这个对象的父级对象传播，从里到外，直至它被处理（父级对象所有同类事件都将被激活），或者它到达了对象层次的最顶层，即document对象（有些浏览器是window）。



## 事件冒泡的作用

> 事件冒泡允许多个操作被集中处理（把事件处理器添加到一个父级元素上，避免把事件处理器添加到多个子级元素上），它还可以让你在对象层的不同级别捕获事件。



## 阻止事件冒泡 

> 事件冒泡机制有时候是不需要的，需要阻止掉，通过 event.stopPropagation() 来阻止

```html
<script>
    $(function(){
        var $father = $('.father');
        var $son = $('.son');
        var $grandson = $('.grandson');
        $father.click(function() {
            alert('father');
        });

        $son.click(function() {
            alert('son');
        });

        $grandson.click(function(event) {
            alert('grandson');
            // 调用下面的代码可以阻止事件冒泡: 
            event.stopPropagation();
        });

        $(document).click(function(event) {
            alert('grandfather');
        });
    })
</script>

    
<div class="father">
    <div class="son">
        <div class="grandson"></div>
    </div>
</div>
```



## 阻止默认行为

> html代码存在某些默认行为, 例如: 我们在页面写一个form表单, 但是我们没有添加表单的事件, 但是这个表单仍然能够进行提交, 这个事件哪里来的呢?  其实是程序设定好的, 所以这些设定好的事件, 我们称为默认行为. 
>
> 我们有时候不需要这些默认行为, 可以对其进行阻止: 
>
> 阻止表单提交

```javascript
$('#form1').submit(function(event){
    event.preventDefault();
})
```





## 合并阻止操作

实际开发中，一般把阻止冒泡和阻止默认行为合并起来写

合并写法可以用:

```javascript
// event.stopPropagation();
// event.preventDefault();

// 合并写法：
return false;
```

 

## 课堂练习

> 需求: 
>
> 1. 开始界面只有一个按钮: 点击按钮后, 打开上层的蒙版和弹窗
> 2. 点击弹窗右上角的 'x' , 弹窗和蒙版关闭
> 3. 点击蒙版层, 弹窗和蒙版关闭
> 4. 点击弹窗内白色的部分, 蒙版层和弹窗不关闭.

![QQ截图20180530201505](../images/QQ%E6%88%AA%E5%9B%BE20180530201505.png)

代码实现: 

```html
<head>
    <script src="js/jquery-1.12.4.min.js"></script>
    <script type="text/javascript">
        $(function(){
            $('#btn01').click(function(){
                $('#pop').show();
                return false;
            })

            $('#shutoff').click(function(){
                $('#pop').hide();
            })
            
            $(document).click(function(){
                $('#pop').hide();
            })

            $('.pop_con').click(function(){
                return false;
            })

        })
    </script>
</head>
<body>
    <input type="button" value="弹出弹框" id="btn01">
    <div class="pop_main" id="pop">

        <div class="pop_con">
            <div class="pop_title">
                <h3>系统提示</h3>
                <a href="#" id="shutoff">×</a>
            </div>
            <div class="pop_detail">
                <p class="pop_text">亲！请关注近期的优惠活动！</p>
            </div>
            <div class="pop_footer">       
            </div>
        </div>
        
        <div class="mask"></div>
    </div>
</body>
```



### 总结:

- 事件冒泡是一种常用的思想
- 事件冒泡是 js 对对象事件的处理策略,是 js 是的一种实现机制
- 事件冒泡:   子元素会把事件, 传递给它的父级, 以此类推
- 我们可以阻止事件冒泡:  使用: stopPropagation( )的方法
- 我们也可以通过调用某些事件, 然后以 return false 的形式,阻止事件冒泡
- 阻止默认行为:   preventDefault( )



