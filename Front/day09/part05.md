# Class的绑定

使用 v-bind 指令来设置元素的 class 属性或者sytle属性，它们的属性值可以是表达式，vue.js在这一块做了增强，表达式结果除了是字符串之外，还可以是对象或者数组。



## 用法一: 可以用一个布尔值控制类名加载

> 控制类名的加载与否

```html
<style>
    .basic {
        color:red;
    }
    .box {
        background-color:red;
    }
    .divbox {
        width:100px;
        height:100px;
    }
</style>


<div id="app">
    <div class="box divbox"></div>
    <div class="basic" v-bind:class="{box:isActive, divbox:isHave}"></div>
</div>


<script>
    var vm = new Vue({
        el:'#app',
        // data属性值如下：
        data:{
            // 控制类名是否加载的变量
             isActive: true,
  			isHave:   false
        }
    })
</script>
```

最终渲染的效果：

```
<div class="static box"></div>
```



## 用法二:  可以给 class 传一个对象

> 对象内部控制类名的加载与否

```html
<style>
    .basic {
        color:red;
    }
    .box {
        background-color:red;
    }
    .divbox {
        width:100px;
        height:100px;
    }
</style>

<div id="app">
    <div class="basic" v-bind:class="{box:isActive, divbox:isHave}"></div>
    <div class="basic" v-bind:class="objName"></div>
</div>

<script>
    var vm = new Vue({
        el:'#app',
        // data属性值如下：
        data:{
            objName:{
                // 控制类名是否加载的变量
                box:false, 
                divbox:true
            }
        }
    })
</script>
```

最终渲染的效果：

```
<div class="static active"></div>
```



例如:

```html
<head>
    <script src="./vue.js"></script>
    <script>
        window.onload = function (){
            var vm = new Vue({
                el:'#app',
                data:{
                    isActive:false,
                    objName:{
                        active:true
                    }
                },
                methods: {
                    // 添加点击事件
                    btnBeclicked:function(){
                        // 属性值取反
                        this.isActive = !this.isActive
                        this.objName.isHave = !this.objName.isHave
                    }
                }
            })
        }
    </script>
    <style>
        .active {
            background-color: pink;
        }
    </style>
</head>
<body>
    <div id="app">
        <button @click="btnBeclicked">按钮控制样式切换</button>
        <div v-bind:class="{active:isActive}">这是第一个div标签</div>
        <div v-bind:class="objName">这是第二个div标签</div>
    </div>
</body>
```





## 用法三:  可以给 class 传一个数组

> 数组内部控制类名的加载与否

```html
<style>
    .basic {
        color:red;
    }
    .box {
        background-color:red;
    }
    .divbox {
        width:100px;
        height:100px;
    }
</style>

<div id="app">
    <div class="basic" v-bind:class="[firstName, secondName]"></div>
</div>

<script>
    var vm = new Vue({
        el:'#app',
        // data属性值如下：
        data:{
            firstName:'box',
            secondName:'divbox'
        }
    })
</script>
```

最终渲染为：

```
<div class="basic box divbox"></div>
```





## 用法四:  可以给 class 传一个三元表达式

> 用三元表达式来控制类名是否加载

> 需要注意:  三元表达式需要写在数组中

```html
<style>
    .basic {
        color:red;
    }
    .box {
        background-color:red;
    }
    .divbox {
        width:100px;
        height:100px;
    }
</style>

<div id="app">
    <div class="basic" v-bind:class="[isActive ? box : '', divbox]"></div>
</div>

<script>
    var vm = new Vue({
        el:'#app',
        // data属性值如下：
        data:{
			isActive: true
        }
    })
</script>
```

最终渲染为：

```
<div class="basic box divbox"></div>
```





不过，当有多个条件class时这样写有些繁琐。所以在数组语法中也可以使用对象语法：

> 这样的使用方法了解即可, 一般我们不会使用

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```



## 总结: 

- 正常使用class:  `class="box divbox"`
- 用变量控制类名的加载与否:  `:class="{box: true/false, divbox}"`
- 上面的式子我们也可以把对象赋一个名字, 调用名字: `:class="objName"`,其中:  `objName={box: true/false}`
- 可以使用数组来包裹类名: `:class=[firstName]`,其中: `firstName="divbox"`
- 也可以使用三元表达式,但是三元表达式需要放在数组中.

