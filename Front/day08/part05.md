# Json

> json 是 JavaScript Object Notation 的首字母缩写，单词的意思是 javascript 对象表示法，这里说的 json 指的是类似于 javascript 对象的一种数据格式对象，目前这种数据格式比较流行，逐渐替换掉了传统的 xml 数据格式。

json 数据对象类似于JavaScript 中2的对象，但是它的键对应的值里面是没有函数方法的，值可以是普通变量，不支持 undefined，值还可以是数组或者 json 对象。

与 JavaScript 对象写法不同的是，json 对象的属性名称和字符串值需要用双引号引起来，用单引号或者不用引号会导致读取数据错误。



## json 格式的数据

```json
{
    "name":"tom",
    "age":18
}
```



json 的另外一个数据格式是数组，和 javascript 中的数组字面量相同。

```json
["tom",18,"programmer"]
```



还可以是更复杂的数据机构：

```json
{
    "name":"jack",
    "age":29,
    "hobby":["reading","travel","photography"]
    "school":{
        "name":"Merrimack College",
        "location":"North Andover, MA"
    },
    "hobby":[
    	{"xuexi":"study"}
    ]
}
```



## json字符串转js对象: 

```javascript
var json_str = '{"name" : "zs", "age" : 20}'
json_obj = JSON.parse(json_str)
console.log(json_obj)
```



## js对象转为 json字符串

```javascript
var json_str = '{"name" : "zs", "age" : 20}'
json_obj = JSON.parse(json_str)
str = JSON.stringify(json_obj)
console.log(str)
```



## 总结: 

- json 是现在市面上普遍使用的数据传输形式.
- json 中如果是字符串, 则需要使用双引号, 如果是数字, 则不需要
- json 的数据和是和对象非常相似, 这点需要注意
- 现在市场上使用 json 比较多的原因是这种数据传输方式非常方便, 简洁.