# 事件委托

> 事件委托就是利用冒泡的原理，把事件加到父级上，通过判断事件来源的子集，执行相应的操作
>
> 事件委托首先可以极大减少事件绑定次数，提高性能；
>
> 其次可以让新加入的子元素也可以拥有相同的操作。



## 一般绑定事件的写法

> 如果我们不使用事件委托,则会写成这样: 

```html
<script>
    $(function(){
        $ali = $('#list li');
        $ali.click(function() {
            $(this).css({background:'red'});
        });
    })
</script>


<ul id="list">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
</ul>
```





## 事件委托的写法

> 使用事件委托, 则可以写成这样: 

```html
<script>
$(function(){
    $ul = $('#list');
    $ul.delegate('li', 'click', function() {
        $(this).css({background:'red'});
    });
})
</script>


<ul id="list">
    <li>1</li>
    <li>2</li>
    <li>3</li>
    <li>4</li>
    <li>5</li>
</ul>
```



## 总结: 

- 事件委托就是将本体不方便做的事情,委托给别的对象
- 事件委托是我们集中处理某些事件的一种方式
- 事件委托用的是delegate方法, 其中的参数为:
	- 第一个参数: 请求委托的对象
	- 第二个参数: 请求委托的事件类型
	- 第三个参数: 请求委托的处理方式(回调函数)

