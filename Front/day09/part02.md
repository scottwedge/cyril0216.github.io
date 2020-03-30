# Vue.js 基本概念

首先通过将vue.js作为一个js库来使用，来学习vue的一些基本概念，我们下载了vue.js后，需要在页面上通过script标签引入vue.js，开发中可以使用开发版本vue.js，产品上线要换成vue.min.js。



## vue.js的使用: 

```html
<!--第一步:  在html页面head部分导入vue框架-->

<script src="js/vue.min.js"></script>
```



> 每个 Vue 应用都需要创建一个新的Vue对象：

```html
<script>
   window.onload = function(){
       // 第二部分:  创建一个vue实例化对象
        var vm = new Vue({
            el:'#app',
            data:{message:'hello world!'}
        });
	}   
</script>
 

<!--
    第三部分: 
    创建一个容器, 内部用于展示vue实例化对象的内容
-->
<div id="app">
	{{ message }}
</div>
```

> 其中，el属性对应一个标签，当vue对象创建后，这个标签内的区域就被vue对象接管，在这个区域内就可以使用vue对象中定义的属性和方法。



## vue对象中数据与方法的处理

当一个 Vue 实例被创建时，它向 Vue 的响应式系统中加入了其data对象中能找到的所有的属性。当这些属性的值发生改变时，视图将会产生“响应”，即匹配更新为新的值。还可以在Vue实例中定义方法，通过方法来改变实例中data对象中的数据，数据改变了，视图中的数据也改变。

```html
<script>
    window.onload = function(){
        var vm = new Vue({
            el:'#app',
            // data中存放的是vue对象中的数据部分: 
            data:{message:'hello world!'},
            // methods里面存放的是vue对象中的方法部分:
            methods:{
                fnChangeMsg:function(){
                    this.message = 'hello Vue.js!';
                }
            }
        });
    }    
</script>


<div id="app">
    <p>{{ message }}</p>
    <button v-on:click="fnChangeMsg">改变数据和视图</button>
</div>
```





## 总结:

- vue使用的时候,必须要通过 new Vue( ) 来实例化一个对象

- vue实例化的对象中, 常见的参数为:  

	- el:     关联 HTML 部分的标签, 使 vue 中的内容能够加载到 HTML里面去
	- data:   页面中需要的数据, 可以通过这个属性进行初始化, 进而赋值到 HTML 页面去
	- methods:   可以给当前vue对象添加方法, 一般我们都会把方法放在这个对象里面
	- computed:   后面会学到, 这个是计算属性, 我们可以给data里面的值添加一些管理,放在这里
	- watch:   如果需要监控data中的某些属性值, 可以在watch中添加监听方法.

- vue 中一般会使用小胡子语法 (插值表达式)

- vue 中添加点击事件使用的不是 onclick ,  而是 v-on:click   大家先记一下, 下面会仔细讲


