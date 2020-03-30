# JavaScript对象

> JavaScript 中的对象，可以理解成是一个键值对的集合，键是调用每个值的名称，值可以是基本变量，还可以是函数和对象。



创建 JavaScript 对象有两种方法:

- 一种是通过顶级 Object 类来实例化一个对象，然后在对象上面添加属性和方法：(此方法不常使用)

```javascript
常用方法:

var person = new Object();

// 添加属性：
person.name = 'tom';
person.age = '25';

// 添加方法：
person.sayName = function(){
    alert(this.name);
}

// 调用属性和方法：
alert(person.age);
person.sayName();
```



- 还可以通过对象直接量的方式创建对象：( 经常使用 )

```javascript
var person2 = {
    name:'Rose',
    age: 18,
    sayName:function(a){
        alert('My name is' + this.name);
    }
}

// 调用属性和方法：
alert(person2.age);
person2.sayName(10);
```



### 总结:

- JS 对象和我们在 python 中了解的对象略有不同, 最主要的区别还是 JS 对象不要类也可以定义
- 我们一般会在 JS 中使用 { } 直接创建对象的形式来使用

