# jquery选择器

## jquery选择器 

> jquery 选择器可以快速地选择元素，选择规则和 css 样式相同，使用 length 属性判断是否选择成功。

```
$('#myId') 			 	    // 选择id为myId的网页元素
$('.myClass') 		 	 	// 选择class为myClass的元素
$('li') 					    // 选择所有的li元素
$('#ul1 li span')   	 	   // 选择id为为ul1元素下的所有li下的span元素
$('input[name="first"]')        // 选择name属性等于first的input元素
```

例如:

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('#app')
            $div.html('hello!!')
            
            var $div1 = $('.box')
            $div1.html('box登场')
            
            var $div2 = $('#divbox')
            $div2.html('会不会把p标签给替换掉?')
            
            var $span = $('ul li p span')
            $span.html('强调强调强调')
            
            var $a = $('a[href="www"]')
            $a.html('a标签修改的文字')
        })
    </script>
</head>
<body>
    <div id="app">nihao</div>
    <div class="box"></div>
    <div id="divbox">
        <p>哈哈,这里是p标签的主场</p>
    </div>
    <a href="com">这是第一个a标签</a>
    <a href="www">这是第二个a标签</a>
    <ul>
        <li>
            <p>这里是p标签内部,添加了<span>强调的字体</span></p>
        </li>
    </ul>
</body>
```





## 对选择集进行过滤

> 我们可以剔除一些不需要的,保留需要的元素

```
$('div').has('p'); 			// 选择包含p元素的div元素
$('div').not('.myClass');	  // 选择class不等于myClass的div元素
$('div').eq(5); 		      // 选择第6个div元素  equal:相等
```

例如: 

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div = $('div').has('p')
            $div.html('哈哈,选中的')
            var $p = $('p').not('.pbox')
            $p.html('更改的是p标签里面的东西')
            var $li = $('li').eq(2)
            $li.html('hahhaa')
        })
    </script>
</head>
<body>
    <div>
        <p>div中的p标签</p>
    </div>
    <div>哈哈哈哈哈</div>
    <p class="pbox">含有pbox的p标签</p>
    <p>纯粹的p标签</p>
    <ul>
        <li>11</li>
        <li>22</li>
        <li>33</li>
        <li>44</li>
    </ul>
</body>
```

效果: 

![QQ截图20180528094239](../images/QQ%E6%88%AA%E5%9B%BE20180528094239.png)





## 选择集转移

```javascript
// 使用说明: 

$('#box').prev();  			//选择id是box的元素前面紧挨的同辈元素
$('#box').prevAll(); 		//选择id是box的元素之前所有的同辈元素
$('#box').next(); 			//选择id是box的元素后面紧挨的同辈元素
$('#box').nextAll(); 		//选择id是box的元素后面所有的同辈元素
$('#box').parent(); 		//选择id是box的元素的父元素
$('#box').children(); 		//选择id是box的元素的所有子元素
$('#box').siblings(); 		//选择id是box的元素的同级元素
$('#box').find('.myClass'); //选择id是box的元素内的class等于myClass的元素
```



例如:

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div1 = $('.box')
            $div1.html('哈哈')  

            var $div2 = $('.box').prev()
            $div2.html('嘿嘿')

            var $div3 = $('.box').next()
            $div3.html('呵呵')

            var $div4 = $('.box').parent()
            $div4.append('哇哇')
        })
    </script>
</head>
<body>
    <div>
        <div>第一个div标签</div>
        <div>第二个div标签</div>
        <div class="box">
            <p>div.box中的p标签</p>
        </div>
        <div>下面倒数第二个div标签</div>
        <div>下面倒数第一个div标签</div> 
    </div>
</body>
```

结果:

![QQ截图20180528100334](../images/QQ%E6%88%AA%E5%9B%BE20180528100334.png)

再例如:

```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div5 = $('.box').siblings()
            $div5.html('good')

            var $div6 = $('.box').find('.pbox')
            $div6.html('呜啊呜啊')
        })
    </script>
</head>
<body>
    <div>
        <div>第一个div标签</div>
        <div>第二个div标签</div>
        <div class="box">
            <p class="pbox">div.box中添加有class的p标签</p>
            <p>div.box中没有添加class的p标签</p>
        </div>
        <div>下面倒数第二个div标签</div>
        <div>下面倒数第一个div标签</div> 
    </div>
</body>
```

结果:

![QQ截图20180528101009](../images/QQ%E6%88%AA%E5%9B%BE20180528101009.png)







## 判断是否选择到了元素

> jquery 有容错机制，即使没有找到元素，也不会出错，可以用 length 属性来判断是否找到了元素, length 等于0，就是没选择到元素，length 大于0，就是选择到了元素。

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
            var $div1 = $('#div1')
            var $div2 = $('#div2')

            // 找到了,   length大于0
            console.log( $div1.length ) 
            // 没有找到, length等于0
            console.log( $div2.length )
        })
    </script>
</head>
<body>
    <div id="div1"></div>
</body>
```

结果:

```
1 
0
```





## 总结:

- 通过使用 jquery 的选择器, 我们会发现选中元素变得更加简单

- 通过这章,我们对标识符有了更深入的了解,  js 中的标识符是可以包含 $ 的, 并且可以以它开头

- 我们可以直接通过某些属性选中需要的元素, 也可以间接通过别的元素选中, 这样的选中方式就是我们说的**选择集转移**

- 选择集过滤, 是为了剔除有相同属性的元素, 这样的技能需要掌握, 公司标签多的时候,会使用到.
