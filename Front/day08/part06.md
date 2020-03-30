# ajax 

> ajax 技术的目的是让 javascript 发送 http 请求，与后台通信，获取数据和信息。
>
> ajax技术的原理是实例化 xml  http 对象，使用此对象与后台通信。
>
> ajax通信的过程不会影响后续javascript的执行，从而实现异步。



## 同步和异步

现实生活中，同步指的是同时做几件事情，异步指的是做完一件事后再做另外一件事，程序中的同步和异步是把现实生活中的概念对调，也就是程序中的异步指的是现实生活中的同步，程序中的同步指的是现实生活中的异步。



## 局部刷新和无刷新

ajax 可以实现局部刷新，也叫做无刷新，无刷新指的是整个页面不刷新，只是局部刷新，ajax可以自己发送http请求，不用通过浏览器的地址栏，所以页面整体不会刷新，ajax获取到后台数据，更新页面显示数据的部分，就做到了页面局部刷新。



## 数据接口

> http://www.weather.com.cn/data/sk/101190408.html?name="123"&age=20

数据接口是后台程序提供的，它是一个 url 地址，访问这个地址，会对数据进行增、删、改、查的操作，最终会返回 json 格式的数据或者操作信息，格式也可以是text、xml等。

例如 :

---

**1\. 查询指定项目属性**
###### 接口功能
> 获取制定项目的分类信息

###### URL
> [http://www.api.com/index.php](www.api.com/index.php)

###### 支持格式
> JSON

###### HTTP请求方式
> GET

###### 请求参数
> | 参数 | 必选 | 类型   | 说明                                    |
> | :--- | :--- | :----- | --------------------------------------- |
> | name | ture | string | 请求的项目名                            |
> | type | true | int    | 请求项目的类型。1：类型一；2：类型二 。 |

###### 返回字段
> | 返回字段 | 字段类型 | 说明                             |
> | :------- | :------- | :------------------------------- |
> | status   | int      | 返回结果状态。0：正常；1：错误。 |
> | company  | string   | 所属公司名                       |
> | category | string   | 所属类型                         |

###### 接口示例
> 地址：[http://www.api.com/index.php?name="可口可乐"&type=1](http://www.api.com/index.php?name="可口可乐"&type=1)
``` javascript
{
    "statue": 0,
    "company": "可口可乐",
    "category": "饮料",
}
```



## $.ajax使用方法

```javascript
// 使用方法: 

$.ajax({
    url:'请求地址',
    type: '请求方式: 默认是'GET'，常用的还有'POST' ',
    dataType: '设置返回的数据格式，常用的是'json'格式，也可以设置为'html' ',
    data: '设置发送给服务器的数据',
    success: '设置请求成功后的回调函数',
    error: '设置请求失败后的回调函数',
    async: '设置是否异步，默认值是'true'，表示异步'
})
```

> 常用参数：
>
> 1、url 			请求地址
> 2、type 			请求方式，默认是'GET'，常用的还有'POST'
> 3、dataType 		设置返回的数据格式，常用的是'json'格式，也可以设置为'html'
> 4、data 			设置发送给服务器的数据
> 5、success 		设置请求成功后的回调函数
> 6、error 			设置请求失败后的回调函数
> 7、async 		设置是否异步，默认值是'true'，表示异步



### 以前的写法：

```
$.ajax({
    url: '/change_data',
    type: 'GET',
    dataType: 'json',
    data:{'code':300268}
    success:function(dat){
        alert(dat.name);
    },
    error:function(){
        alert('服务器超时，请重试！');
    }
});
```

### 新的写法(推荐)：

```
$.ajax({
    url: '/change_data',
    type: 'GET',
    dataType: 'json',
    data:{'code':300268}
})
.done(function(dat) {
    alert(dat.name);
})
.fail(function() {
    alert('服务器超时，请重试！');
});

```

例如:

```html
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        // {"movieType":"in_theaters","pageIndex":2,"start":0,"count":10}
        $(function(){
            $.ajax({
                url:'http://localhost:3008/getMovieListData?message={"movieType":"in_theaters","pageIndex":2,"start":0,"count":10}',
                type:'get',
                dataType:'json'
            }).done(function(data){
                console.log(data)
            }).fail(function(error){
                
            })
        })
    </script>
```



### $.ajax的简写方式

`$.ajax`按照请求方式可以简写成`$.get`或者`$.post`方式

```javascript
$.get("/change_data", {'code':300268},
  function(dat){
    alert(dat.name);
});

$.post("/change_data", {'code':300268},
  function(dat){
    alert(dat.name);
});
```





## 同源策略

ajax 请求的页面或资源只能是同一个域下面的资源，不能是其他域的资源，这是在设计 ajax 时基于安全的考虑。特征报错提示：

```
XMLHttpRequest cannot load https://www.baidu.com/. No  
'Access-Control-Allow-Origin' header is present on the requested resource.  
Origin 'null' is therefore not allowed access.
```



## 获取豆瓣电影数据 --- 课堂实例

```html
<head>
    <script src="./jquery-1.12.4.min.js"></script>
    <script>
        $(function(){
             $('.btn').click(function(){
             
                $.ajax({
                    url:'https://api.douban.com/v2/movie/in_theaters?start=0&count=10',
                    type:'get',
                    dataType:'jsonp',
                }).done(function(data){
                    console.log(data)
                    var array = data.subjects
                    $('.list').append(array[0].title)
                })
            })
        })
    </script>
</head>
<body>
    <div class="btn">点此获取电影数据</div>
    <ul class="list"></ul>
</body>
```

一般 ajax 数据接口和 jsonp 数据接口的区别

> 开发返回数据的接口，如果是一般的 ajax 接口，让接口直接返回 json 格式的数据字符串就可以了，这种接口的数据是不能跨域请求的，如果要跨域请求数据，需要开发jsonp的接口，开发jsonp的接口，需要获取请求地址中的参数，也就是'callback'键对应的值，然后将这个值和json数据拼装成一个函数调用的形式的字符串返回，就完成了一个jsonp的接口，这个键值对是由$.ajax函数自动产生的。比如：'callback'键对应的值一般是这样的：jQuery1124018787969015631711_1522330257607，所以：

- 一般接口返回的数据形式：

	'{"iNum":12,'sTr':'abc'}';

- jsonp返回的数据形式：

	'jQuery1124018787969015631711_1522330257607({"iNum":12,"sTr":"abc"})';



## 总结:

- 这章的概念比较多, 但是我们不需要都了解掌握, 我们只需要能够简单的使用 $.ajax即可
- 现在市面上数据请求的形式比较多, 普遍有 get  post  jsonp
- 同步和异步的概念也要明白,  异步指的是多个步骤同时进行.   同步指的是多个步骤一步一步执行.

