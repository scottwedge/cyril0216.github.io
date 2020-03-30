# 表单验证

## 正则表达式 

> 能让计算机读懂的字符串匹配规则。



## 正则表达式的写法

```javascript
使用方法: 


// 定义一个正则表达式(两种方法)
var re = new RegExp('规则', '可选参数');


var re = /规则/参数;


// 测试正则表达式: 
var result = re.test(要测试的字符串)
// result  --->  true  
// result  --->  false 
```





## 规则中的字符

### 普通字符匹配

```
规则使用方式:

/a/                  匹配字符 ‘a’  
/a,b/ 				匹配字符 ‘a,b’
```



## 转义字符匹配

```
规则使用方式: 



\d 		匹配一个数字，即0-9
\D 		匹配一个非数字，即除了0-9
\w 		匹配一个单词字符（字母、数字、下划线）
\W 		匹配任何非单词字符。单词字符包括：a-z、A-Z、0-9，以及下划线。
\s 		匹配一个空白符
\S 		匹配一个非空白符
. 		匹配一个任意字符
```

例如: 

```javascript
var sTr01 = '123456asdf';
var re01 = /\d+/;
//匹配纯数字字符串
var re02 = /^\d+$/;

alert(re01.test(sTr01)); //弹出true
alert(re02.test(sTr01)); //弹出false
```



## 量词 

### 对左边的匹配字符定义个数

```
规则使用方式: 


? 			出现零次或一次（最多出现一次）
+ 			出现一次或多次（至少出现一次）
* 			出现零次或多次（任意次）
{n} 		出现n次
{n,m} 		出现n到m次
{n,} 		至少出现n次
```





## 任意一个或者范围

```
[abc123] : 	匹配‘abc123’中的任意一个字符

[a-z0-9] : 	匹配a到z或者0到9中的任意一个字符
```



## 限制开头结尾 

```
^ 		以紧挨的元素开头

$ 		以紧挨的元素结尾
```



## 修饰参数

```
g： 		global: 全文搜索，默认搜索到第一个结果接停止

i： 		ingore case: 忽略大小写，默认大小写敏感


例如:    var re = /规则/i
```



**案例:**

```javascript
//var str = 'a'
var str = 'A'
var re = /a/i

var result = re.test(str) 
console.log(result)
```



## 常用函数 test

```
使用方法: 

正则.test(字符串) 

// 匹配成功，就返回真，否则就返回假
```



## 正则默认规则 

匹配成功就结束，不会继续匹配，区分大小写



### 常用正则规则   

```javascript
// 用户名验证：(数字字母或下划线6到20位)
var reUser = /^\w{6,20}$/;

// 邮箱验证：    
var reMail = /^[a-z0-9][\w\.\-]*@[a-z0-9\-]+(\.[a-z]{2,5}){1,2}$/i;

// 密码验证：
var rePass = /^[\w!@#$%^&*]{6,20}$/;

// 手机号码验证：
var rePhone = /^1[3456789]\d{9}$/;
```

例如: 

```html
<script src="./jquery-1.12.4.min.js"></script>
<script>
    // var str = 'helloA world'
    // var reg = /a/i

    var str = 'a123ab'
    var reg = /^\d/

    var result = reg.test(str)
    alert(result)
</script>
```



## 注册页面表单验证 --- 课堂实例

注册页面表单验证

![QQ截图20180530164522](../images/QQ%E6%88%AA%E5%9B%BE20180530164522.png)

实例代码: 

```javascript
$(function(){
    // 定义一个变量
    // 记录用户输入的用户名是否有错
    // true   有错  false  没错
    var isNameHaveError = true

    // 获取用户名输入框
    var $input1 = $('#user_name')

    // 当用户输入完信息后调用这个方法进行验证.
    $input1.blur(function(){

        // 定义一个验证的方法
        checkUserNameFunc()

    }).click(function(){
        // 输入框点击之后调用这个方法
        // 用户点击输入框后,需要把验证的提示信息隐藏
        $(this).next().hide()
    })


    // 进行表单中用户名的验证方法
    function checkUserNameFunc(){
        // 获取用户输入的信息
        var value = $input1.val()
        // 定义正则表达式
        var reUser = /^\w{6,20}$/;
        // 判断信息是否为空
        if (value == '') {
            // 如果为空, 显示span, 展示提示信息
            $input1.next().html('用户名不能为空1111').show()
            isNameHaveError = false
            return
        }

        // 判断输入字符是否符合正则表达式的要求
        if (reUser.test(value)) {
            // 用户名格式没有出错
            isNameHaveError = false
        } else {
            isNameHaveError = true
             // 如果格式出错, 显示span, 展示提示信息
             $input1.next().html('用户名是6-20位数字,字母或下划线').show()
        }
    }
})
```

