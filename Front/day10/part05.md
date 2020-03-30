## 自定义指令

> 指令是用来做dom操作的，如果vue现有的指令不能满足开发要求，我们需要对普通DOM元素进行底层操作，这时候就会用到自定义指令。

定义一个全局指令，让input框自动获取焦点

```html
<script>
	Vue.directive('focus',{
      inserted:function(el){
        el.focus();
        el.style.background = 'gold';
      }     
    })
    
    var vm = new Vue({
        el:'#app',
        data:{}
    })
</script>


<div id="app">    
  <input type="text" v-focus>
</div>
```

如果定义成 vue 对象局部的，可以用vue对象的directives属性：

```javascript
var vm = new Vue({
    el:'#app',
    data:{},
    directives: {
      focus: {
        inserted: function(el) {
          el.focus();
          el.style.background = 'gold';
        }
      }
    }
})
```



案例:

```html
<head>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app">
        <input type="text" v-fodc>
        <div v-fodd> sss</div>
    </div>
    <script>
        Vue.directive('fodd',{
            inserted:function(el){
                el.style.width = '100px'
                el.style.height = '100px'
                el.style.border = '1px solid gold'
            }
        })
        var vm = new Vue({
            el:'#app',
            directives:{
                fodc:{
                    inserted:function(el){
                        el.focus()
                        el.style.background = 'gold'
                    }
                }
            }
        })

    </script>
</body>
```



## 总结: 

- 自定义指令指的就是我们自己制作自己想要的指令效果
- 每个插件都会给予用户一些自主权限, 让使用者自由发挥, 以满足公司不同的业务需要
- directive(自定义指令)  和上面的过滤器一样,也有两种, 分别为内部和外部,  这两个不同点就是名字和书写格式上, 希望下去后多多练习.

