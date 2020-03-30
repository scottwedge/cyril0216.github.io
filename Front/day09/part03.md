# Vue 模板语法

> 模板语法指的是如何将数据放入 html 中，Vue.js使用了基于 HTML的模板语法，允许开发者声明式地将DOM绑定至底层 Vue 实例的数据。所有 Vue.js的模板都是合法的 HTML ，所以能被遵循规范的浏览器和 HTML 解析器解析。



## 小胡子语法

> 小胡子语法也被称为差值表达式

数据绑定最常见的形式就是使用“Mustache”语法 (双大括号) 的文本插值：

```html
<!--常见写法: -->


<span> {{ msg }} </span>

{{ number + 1 }}

{{ ok ? 'YES' : 'NO' }}

{{ message.split('').reverse().join('') }}
```



## 指令

> 指令 (Directives) 是带有“v-”前缀的特殊属性。
>
> 指令属性的值预期是单个JavaScript表达式，指令的职责是，当表达式的值改变时，将其产生的连带影响，响应式地作用于DOM。
>
> 常见的指令有v-bind、v-if、v-on。

```html
<!-- 根据ok的布尔值来插入/移除 <p> 元素 -->
<p v-if="ok">是否显示这一段</p>

<!-- 监听按钮的click事件来执行fnChangeMsg方法 -->
<button v-on:click="fnChangeMsg">按钮</button>
```



> 如果是标签的属性要使用值，就不能使用“Mustache”语法，需要写成使用v-bind指令：

```html
<a v-bind:href="url" v-bind:title='tip'>百度网</a>
```



## 缩写

v-bind和v-on事件这两个指令会经常用，所以有简写方式：

```html
v-bind的简写: 

<!-- 完整语法: -->
<a v-bind:href="url">...</a>

<!-- 缩写为:  -->
<a :href="url">...</a>



v-on:的简写: 
<!-- 完整语法: -->
<button v-on:click="fnChangeMsg">按钮</button>

<!-- 缩写为:  -->
<button @click="fnChangeMsg">按钮</button>
```



## 总结:

- 我们可以通过小胡子语法向 HTML 中添加数据
- vue 中有指令的概念:  以 v- 开头 ,这里是参考的 angular  框架,  angular 框架以: ng- 开头
- v-bind:   这个方法是绑定的意思,  主要是把 data 中的属性和 HTML 页面中的属性值绑定到一起
- 事件在 vue 中:   v-on:  click 表示.            可以简写成:  @click             
	- 简写的形式经常使用, 但是需要了解 v-on:的意义
- v-bind:  绑定方法也可以简写:  简写为`:`
- 小胡子语法中可以进行简单的逻辑运算,这个属于了解层次, 知道就够了, 一般不会这样用

