# 条件渲染

通过条件指令可以控制元素的创建(显示)或者销毁(隐藏)，常用的条件指令如下：

## v-if

v-if可以控制元素的创建或者销毁

```html
<h1 v-if="ok">Yes</h1>
```





## v-else

v-else指令来表示 v-if 的“else 块”，v-else 元素必须紧跟在带 v-if 或者 v-else-if 的元素的后面，否则它将不会被识别。

```html
<div v-if="Math.random() > 0.5">
  Now you see me
</div>
<div v-else>
  Now you don't
</div>
```





## v-else-if

v-else-if，顾名思义，充当 v-if 的“else-if 块”，可以连续使用：

```html
<div v-if="type === 'A'">
  A
</div>
<div v-else-if="type === 'B'">
  B
</div>
<div v-else-if="type === 'C'">
  C
</div>
<div v-else>
  Not A/B/C
</div>
```

## v-show

另一个用于根据条件展示元素的选项是 v-show 指令。用法和v-if大致一样，但是它不支持v-else,它和v-if的区别是，它制作元素样式的显示和隐藏，元素一直是存在的：

```html
<h1 v-show="ok">Hello!</h1>
```



案例: 

```html
<head>
    <script src="js/vue.js"></script>
</head>
<body>
    <div id="app">
        <div v-if="ok">使用v-if的div标签</div>
        <div v-else>使用v-else的div标签，要和v-if写在一起</div>

        <div v-if="type=='A'">
            A
        </div>
        <div v-else-if="type=='B'">
            B
        </div>
        <div v-else-if="type=='C'">
            C
        </div>
        <div v-else>
            Not A/B/C
        </div>

        <input type="button" value="改变字母" @click="fnChange">
        <p v-if="ok">使用v-if的p标签</p>
        <p v-show="ok">使用v-show的p标签</p>
    </div>
    
    <script>
        var vm = new Vue({
            el:'#app',
            data:{
                ok:true,
                type:'B'
            },
            methods:{
                fnChange:function(){
                    this.type = 'D';
                }
            }
        })
    </script>
</body>
```



### 总结: 

- vue 这个框架会使我们使用非常方便, 所以它推出了很多的指令, 这些指令能够帮助我们快速方便的书写代码
- v-if  能够决定元素是否显隐, 如果隐藏, 则不会在 DOM 树中存在, 即一旦使用 v-if   隐藏某个元素,其实是销毁当前元素.
- v-else  一般和 v-if 配合使用, 不能够单独使用, 这一点请大家牢记
- v-show  和 v-if 的功能类似, 都能够控制元素的显隐, 但是它是通过样式: display: none;  来控制元素的, 而不像 v-if 是通过销毁与创建来控制.
- v-show  和 v-if  使用哪个的问题:  随意, 想使用哪个就使用哪个, 这个没有强制的要求, 只要能够达到目的就可以. 如果有的选,还是使用 v-show 好点, 不会频繁的操作 DOM 树.