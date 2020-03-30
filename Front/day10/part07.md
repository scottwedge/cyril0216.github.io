# 数据交互

> vue.js没有集成 ajax 功能，要使用 ajax 功能，可以使用vue官方推荐的 axios.js 库来做ajax的交互。 axios库的下载地址：<https://github.com/axios/axios/releases>



## axios完整写法：

```
axios({
  method: 'post',
  url: '/user/12345',
  data: {
    firstName: 'Fred',
    lastName: 'Flint2stone',
    start:0,
    count:10
  }
});
```

axios的请求也分为get请求和post请求。



## 执行get请求

```javascript
// 为给定 ID 的 user 创建请求
// then是请求成功时的响应，catch是请求失败时的响应

axios.get('/user?ID=12345&start=0&count=10')
.then(function (response) {
  console.log(response);
})
.catch(function (error) {
  console.log(error);
});


// 可选地，上面的请求可以这样做
axios.get('/user', {
  params: {
    ID: 12345,
    start:0,
    count:10
  }
})
.then(function (response) {
  console.log(response);
})
.catch(function (error) {
  console.log(error);
});
```

## 执行post请求

```javascript
axios.post('/user', {
  firstName: 'Fred',
  lastName: 'Flintstone'
})
.then(function (response) {
  console.log(response);
})
.catch(function (error) {
  console.log(error);
});
```

案例:

```html
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <meta http-equiv="X-UA-Compatible" content="ie=edge">
    <title>Document</title>
    <script src="./vue.js"></script>
    <script src="./axios.min.js"></script>
    <script>
        window.onload = function () {
            var vm = new Vue({
                el: '#app',
                methods: {
                    getFunc: function () {
                        axios.get(
                                'http://localhost:3008/getMovieListData?message={"movieType":"in_theaters","pageIndex":2,"start":0,"count":10}'
                            ).then(function (data) {
                                // document.write(data)
                                var div = document.getElementById('box')
                                // div.innerHTML = data
                                console.log(data.data.subjects)
                                div.innerHTML = data.data.subjects[0].title 
                            })
                            .catch(function (error) {
                                alert(error)
                            })
                    }
                }
            })
        }
    </script>
</head>

<body>
    <div id="app">
        <button @click="getFunc">按钮</button>
        <div id="box"></div>
    </div>
</body>
```



## 总结: 

- axios 不是vue作者写的框架, 而是 github 上面开源的第三方框架, vue作者自己写的框架是vue-resource.  但是因为不如axios好用, 所以最终作者推荐大家使用这个框架来获取数据.
- axios  只有get 和 post 方法, 不支持jsonp.  也就是说axios框架不能够跨域请求数据,希望大家注意这一点.
- 请求分为两种 get 和 post 

