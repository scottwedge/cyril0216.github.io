# 过滤器

> Vue.js 允许你自定义过滤器，可被用于一些常见的文本格式化。过滤器可以用在两个地方：双花括号插值和 v-bind 表达式

```html
<!-- 在双花括号中 -->
{{ prize | RMB }}
```

过滤器实际上是一个函数，可以在一个组件的选项中定义组件内部过滤器：

```javascript
var vm = new Vue({
    el:'#app',
    data:{
        prize:10000
    },
    filters:{
      RMB:function(value){
        if(value=='')
        {
          return;
        }
        return '¥ '+value;
      }
    }
})
```



或者在创建 Vue 实例之前全局定义过滤器：

```html
<!-- 在v-bind中 -->
<div v-bind:id="prize | Yuan"></div>
```

实现: 

```javascript
Vue.filter('Yuan',function(value){
  if(value=='')
  {
    return;
  }
  return value+'元';
});

var vm = new Vue({
    el:'#app',
    data:{
        prize:10000
    }
})
```



此时过滤器'RMB'只能在定义它的对象接管标签内使用，而'Yuan'可以全局使用

案例:

```html
<head>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app">
        {{ msg | upper() }}
        {{ title | lower() }}
       <div type="text" v-bind:id="flag | rever()">这是个div标签</div>
    </div>

    <script>
        Vue.filter('rever', function (value) {
            return value.split('').reverse().join('')
        })
        var vm = new Vue({
            el:'#app',
            data:{
                msg: 'abcdefg',
                title: 'ABDEEDJSOIFDOS',
                flag: 'hello world'
            },
            filters: {
                 upper :function(value){

                     return value.toUpperCase()
                 },
                 lower : function (value){
                     return value.toLowerCase()
                 }
            }
        })
    </script>
</body>
```



# 总结: 

- filter 是 vue 中的过滤器, 过滤器顾名思义就是把数据进行过滤, 得到过滤的数据的作用.
- 在 vue 中, 过滤器有两种写法, 一种是写在 vue 对象内部的过滤器, 另一种是写对象外部的过滤器
	- 其中写在内部的过滤器:   以  filters 开头, 且内部都是方法
	- 写在外部的过滤器, 以 filter 开头, 且过滤器名称和方法分开
- 过滤器在vue中也是一个非常重要的部分, 但是具体的使用还是需要结合公司的情况.
- 从 vue 2.x 开始, 过滤器推出了与 v-bind  结合使用的情况, 所以这个地方需要大家注意

