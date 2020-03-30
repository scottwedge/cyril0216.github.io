# ES6语法

> ES6 是 JavaScript 语言的新版本，它也可以叫做 ES2015，之前学习的 JavaScript 属于 ES5，ES6 在它的基础上增加了一些语法，ES6 是未来 JavaScript 的趋势，而且 vue 组件开发中会使用很多的ES6 的语法，所以掌握这些常用的 ES6 语法是必须的。

## 声明 let 和 const

> let 和 const 是新增的声明变量的开头的关键字，在这之前，变量声明是用 var 关键字，这两个关键字和 var 的区别是，它们声明的变量没有预解析，let 和 const 的区别是，let 声明的是一般变量，const 申明的常量，不可修改。

### let

```
使用格式:

let age = 20
```

> age的值可以改变, 但是使用 let 定义的变量没有预解析功能

### const

```
使用格式:

const age = 23
```

> age的值不能够改变, 否则报错



案例:

```javascript
alert(iNum01) 			// 弹出undefined
// alert(iNum02); 		报错，let 关键字定义变量没有变量预解析
// alert(iNum03); 		报错，const 关键字定义变量没有变量预解析

var iNum01 = 6;
// 使用let关键字定义变量
let iNum02 = 12;
// 使用const关键字定义变量
const iNum03 = 24;

alert(iNum01); // 弹出6
alert(iNum02); // 弹出12
alert(iNum03); // 弹出24

iNum01 = 7;
iNum02 = 13;
//iNum03 = 25; 			// 报错,const 定义的变量不可修改,const 定义的变量是常量

alert(iNum01)
alert(iNum02); 
alert(iNum03);
```



## 箭头函数

> 可以把箭头函数理解成匿名函数的第二种写法

```javascript
// 定义函数的一般方式
function fnRs(a,b){
    var rs = a + b;
    alert(rs);
}
fnRs(1,2);        


// 通过匿名函数赋值来定义函数
var fnRs = function(a,b){
    var rs = a + b;
    alert(rs);
}
fnRs(1,2);


// 通过箭头函数的写法定义
var fnRs = (a,b)=>{
    var rs = a + b;
    alert(rs);
}        
fnRs(1,2)

// 一个参数可以省略小括号
var fnRs2 = a =>{
    alert(a);
}
fnRs2('haha!');


// 箭头函数的作用，可以绑定对象中的this
var person = {
    name:'tom',
    age:18,
    showName:function(){
        setTimeout(()=>{
            alert(this.name);
        },1000)            
    }
}
person.showName();
```

案例:

```html
<head>
    <script src="./vue.js"></script>
</head>
<body>
    <div id="app">
        {{name}}
        <div @click="func">按钮</div>
        <div @click="fun1(10,11)">annana</div>
    </div>
    <script>
        var vm = new Vue({
            el:'#app',
            data:{
                name:'hello'
            },
            methods:{
                func:function(){
                    setTimeout(() => {
                        this.name = 'hello world' 
                    }, 3000);
                },
                fun1:(a,b)=>{
                    alert(a+b)
                }
            }
        })
    </script>
</body>
```



## 模块导入 import 和导出 export

> javascript 之前是没有模块的功能的，之前做 js 模块化开发，是用的一些 js 库来模拟实现的，在 ES6中加入了模块的功能，和 python 语言一样，python 中一个文件就是一个模块，ES6 中，一个 js 文件就是一个模块，不同的是，js 文件中需要先导出( export )后，才能被其他 js 文件导入( import )

```javascript
// model.js文件中导出
var person = {name:'tom',age:18}
export default {person}

// index.js文件夹中导入
import person from 'js/model.js'

// index.js中使用模块
person.name
person.age

/*
上面导出时使用了default关键字，如果不使用这个关键字，导入时需要加大括号：
import {person} from 'js/model.js'
*/
```

目前 ES6 的模块功能需要在服务器环境下才可以运行。



## 对象的简写

javascript 对象在 ES6 中可以做一些简写形式，了解这些简写形式，才能方便我们读懂一些在javascript 代码中简写的对象。

```javascript
let name = '李思';
let age = 18;

/*
var person = {
    name:name,
    age:age,
    showname:function(){
        alert(this.name);
    },
    showage:function(){
        alert(this.age);
    }
}
*/

// 简写成下面的形式
var person = {
    name,
    age,
    showname(){
      alert(this.name);
    },
    showage(){
      alert(this.age);
    }
}

person.showname();
person.showage();
```



## 总结:

- import 和 export 我们在后面学习 vue 组件的时候会学习到
- 对象的简写大家知道就可以,一般不会这样使用
- 箭头函数偶尔会使用, 大家要学会使用 
- let  和  const 不是必须掌握的内容, 能掌握更好, 不能的话就全部写成 var 也是没有问题的.

