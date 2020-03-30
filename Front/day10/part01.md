# 列表渲染

> 通过 v-for 指令可以将一组数据渲染到页面中，数据可以是数组或者对象，v-for 指令需要使用 item in items 形式的特殊语法，items 是源数据数组并且 item 是数组元素迭代的别名。



## 遍历数组

```html
<ul id="example-1">
  <li v-for="item in items">
    {{ item}}
  </li>
</ul>
```

vue对象创建如下：

```javascript
var example1 = new Vue({
  el: '#example-1',
  data: {
    items: ['foo','bar']
  }
})
```

如果想加上索引值，可以加上第二个参数

```html
<ul id="example-2">
  <li v-for="(item, index) in items">
    {{ index }} - {{ item.message }}
  </li>
</ul>
```



## 遍历对象

> 可以通过 v-for 遍历一个对象, 获取对象中的 value 值

```html
<ul id="v-for-object">
  <li v-for="value in objects">
    {{ value }}
  </li>
</ul>
```

除了对象中的 value 值之外, 还可以获取对象中的元素的索引值

```html
<ul id="v-for-object">
  <li v-for="(value,key) in objects">
    {{ key }}-{{ value }}
  </li>
</ul>
```

如果想再加上对象的 key 值, 也是可行的

```html
<div v-for="(value, key, index) in object">
  {{ index }}. {{ key }}: {{ value }}
</div>
```



```javascript
new Vue({
  el: '#v-for-object',
  data: {
    objects: {
      firstName: 'John',
      lastName: 'Doe',
      age: 30
    }
  }
})
```

案例: 

```html
<head>
    <script src="js/vue.js"></script>
</head>
<body>
    <div id="app">
        <ul>
            <li v-for="item in movieName">
                {{ item }}
            </li>
        </ul>

        <ul>
            <li v-for="(item,index) in movieName">
                {{ index+1 }}、{{ item }}
            </li>
        </ul>

        <ul>
            <li v-for="val in peron01">
                {{ val }}
            </li>
        </ul>

        <ul>
            <li v-for="(val,key) in peron01">
                {{ key }}:{{ val }}
            </li>
        </ul>

    </div>

    <script>
        var vm = new Vue({
            el:'#app',
            data:{
                movieName:['古墓丽影3','唐人街探案2','芳华','环太平洋2','大话西游5'],
                peron01:{'name':'李思','age':18,'gender':'男'}
            }
        })
    </script>
</body>
```



## 总结:

- 通过这章的学习, 我们知道 vue 框架中给我们定义了一些标签的属性, 通过这些属性我们可以快速的构建项目的整体结构
- v-for 就和我们python中学习的一样, 通过 for .....  in.... 的形式将将 data 中的数据, 遍历加载到 DOM 元素中
- vue 框架也可以使用 v-for 遍历对象 例如:  使用 v-for 遍历对象, 将对象中的数据也可以遍历出来.