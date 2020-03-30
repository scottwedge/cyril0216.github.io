## jquery链式调用

> jquery 对象的方法会在执行完后返回这个 jquery 对象，所有 jquery 对象的方法可以连起来写：

```
$('#div1') 				 //  id为div1的元素
.children('ul') 		 //	该元素下面的ul子元素
.slideDown() 		     //	高度从零变到实际高度来显示ul元素
.parent()  				//	跳到ul的父元素，也就是id为div1的元素
.siblings()  			//	跳到div1元素平级的所有兄弟元素
.children('ul') 		//	这些兄弟元素中的ul子元素
.slideUp(); 		    //	高度实际高度变换到零来隐藏ul元素
```



## 层级菜单 ---- 课堂练习

![QQ截图20180604222010](../images/QQ%E6%88%AA%E5%9B%BE20180604222010.png)

层级菜单案例代码: 

```html
<!DOCTYPE html>
<html lang="en">
<head>
	<meta charset="UTF-8">
	<title>层级菜单</title>	
	<style type="text/css">
		body{
			font-family:'Microsoft Yahei';
		}
		body,ul{
			margin:0px;
			padding:0px;
		}
		
		ul{list-style:none;}


		.menu{
			width:200px;
			margin:20px auto 0;
		}

		.menu .level1,.menu li ul a{
			display:block;
			width:200px;
			height:30px;
			line-height:30px;
			text-decoration:none;
			background-color:#3366cc;
			color:#fff;
			font-size:16px;
			text-indent:10px;			
		}

		.menu .level1{
			border-bottom:1px solid #afc6f6;
			
		}

		.menu li ul a{
			font-size:14px;
			text-indent:20px;
			background-color:#7aa1ef;
					
		}

		.menu li ul li{
			border-bottom:1px solid #afc6f6;
		}

		

		.menu li ul{
			display:none;
		}

		.menu li ul.current{
			display:block;
		}

		.menu li ul li a:hover{
			background-color:#f6b544;
		}


	</style>
	
	<script src="./jquery-1.12.4.min.js"></script>
	<script type="text/javascript">
		$(function(){
			var $a = $('.level1')
			$a.click(function(){
				$(this).next().stop().slideToggle().parent().siblings().children('ul').slideUp()
			})
		})
	</script>
</head>
<body>
	<ul class="menu">
		<li>
			<a href="#" class="level1">手机</a>
			<ul class="current">
				<li><a href="#">iPhone X 256G</a></li>
				<li><a href="#">红米4A 全网通</a></li>
				<li><a href="#">HUAWEI Mate10</a></li>
				<li><a href="#">vivo X20A 4GB</a></li>
			</ul>
		</li>
		<li>
			<a href="#" class="level1">笔记本</a>
			<ul>
				<li><a href="#">MacBook Pro</a></li>
				<li><a href="#">ThinkPad</a></li>
				<li><a href="#">外星人(Alienware)</a></li>
				<li><a href="#">惠普(HP)薄锐ENVY</a></li>
			</ul>
		</li>
		<li>
			<a href="#" class="level1">电视</a>
			<ul>
				<li><a href="#">海信(hisense)</a></li>
				<li><a href="#">长虹(CHANGHONG)</a></li>
				<li><a href="#">TCL彩电L65E5800A</a></li>				
			</ul>
		</li>
		<li>
			<a href="#" class="level1">鞋子</a>
			<ul>
				<li><a href="#">新百伦</a></li>
				<li><a href="#">adidas</a></li>
				<li><a href="#">特步</a></li>
				<li><a href="#">安踏</a></li>
			</ul>
		</li>
		<li>
			<a href="#" class="level1">玩具</a>
			<ul>
				<li><a href="#">乐高</a></li>
				<li><a href="#">费雪</a></li>
				<li><a href="#">铭塔</a></li>
				<li><a href="#">贝恩斯</a></li>
			</ul>
		</li>
		
	</ul>
</body>
</html>
```

