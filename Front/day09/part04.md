## 计算属性和侦听属性

#### 计算属性

模板内的表达式非常便利，但是设计它们的初衷是用于简单运算的。在模板中放入太多的逻辑会让模板过重且难以维护。例如：

```html
<div id="example">
  {{ message.split('').reverse().join('') }}
</div>
```

这个表达式的功能是将message字符串进行反转，这种带有复杂逻辑的表达式，我们可以使用计算属性

```html
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>
      Computed reversed message: "{{ reversedMessage }}"       	 </p>
</div>



<script>
    var vm = new Vue({
      el: '#example',
      data: {
        message: 'Hello'
      },
      computed: {
        // 计算属性的 getter
        reversedMessage: function () {
          // `this` 指向 vm 实例
          return 			this.message.split('').reverse().join('')
        }
      }
    })
</script>
```





#### 监听属性

侦听属性的作用是侦听某属性值的变化，从而做相应的操作，侦听属性是一个对象，它的键是要监听的对象或者变量，值一般是函数,当你侦听的元素发生变化时，需要执行的函数，这个函数有两个形参，第一个是新值,   第二个是旧值。

```html
<head>
    <script src="./vue.js"></script>
    <script>
        window.onload = function (){
            var vm = new Vue({
                el:'#app',
                data:{
                    msg:'haha'
                },
                watch: {
                    msg:function(newvalue,oldvalue){
                        alert('哈哈')
                    }
                }
            })
        }
    </script>
</head>
<body>
    <div id="app">
        <input type="text" v-model="msg">
        {{ msg }}
    </div>
</body>
```



#### 总结: 

- 计算属性总体来说就是为了把一些data中属性的逻辑运算移除小胡子以外, 这是 vue 作者针对这个框架进行的优化, 作者推荐这样使用, 但是不会的话可以不用. 没有实际意义
- 监听属性,最主要的就是记住 watch  这个关键字在所有的编程语言中差不多都是监听,监视的意思.  通过这个属性, 我们可以得到别的属性什么时候发生了变化, 这样对于我们来说非常重要, 在公司, 很多时候会用到这个方法.  watch 监听的属性,  属性一旦发生变化,就会调用对应的方法, 方法中有两个参数, 一个是老的值, 一个是新值.

