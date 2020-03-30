# Gunicorn

Gunicorn（绿色独角兽）是一个Python WSGI的HTTP服务器。从Ruby的独角兽（Unicorn ）项目移植。该Gunicorn服务器与各种Web框架兼容，实现非常简单，轻量级的资源消耗。Gunicorn直接用命令启动，不需要编写配置文件，相对uWSGI要容易很多。

#### 安装gunicorn

```
pip install gunicorn
```

**查看命令行选项：** 安装gunicorn成功后，通过命令行的方式可以查看gunicorn的使用信息。

```bash
$gunicorn -h
```

**直接运行：**

```bash
#直接运行，默认启动的127.0.0.1::8000
gunicorn 运行文件名称:Flask程序实例名
```

**指定进程和端口号：** -w: 表示进程（worker）。 -b：表示绑定ip地址和端口号（bind）。

```bash
$gunicorn -w 4 -b 127.0.0.1:5001 运行文件名称:Flask程序实例名
```

