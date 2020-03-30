# 头条聊天服务实现

在toutiao-backend/im目录中创建server.py

```python
import socketio

# 创建sio对象
sio = socketio.Server(async_mode='eventlet')
app = socketio.Middleware(sio)
```

在toutiao-backend/im目录中创建im服务启动程序main.py

运行方式`python main.py [端口]`，如`python main.py 8000`

```python
import eventlet
eventlet.monkey_patch()

import eventlet.wsgi
import sys

# 获取命令行参数，目的是想让im服务运行的端口在启动程序时指定
if len(sys.argv) < 2:
    print('Usage: python main.py [port]')
    exit(1)

port = int(sys.argv[1])

from server import app
import notify

# socketio服务器运行的地址
SERVER_ADDRESS = ('', port)

# 启动socketio服务器
sock = eventlet.listen(SERVER_ADDRESS)
eventlet.wsgi.server(sock, app)
```

在toutiao-backend/im目录中创建chat.py

```python
from server import sio
import time


@sio.on('connect')
def on_connect(sid, environ):
    """
    与客户端建立好连接后被执行
    """
    print('sid={}'.format(sid))
    print('environ={}'.format(environ))
    msg_data = {
        'msg': 'hello',
        'timestamp': round(time.time()*1000)
    }
    sio.emit('message', msg_data, room=sid)


@sio.on('message')
def on_message(sid, data):
    """
    接收message事件消息时执行
    """
    print('sid={} data={}'.format(sid, data))
    msg_data = {
        'msg': 'I have received your msg: {}'.format(data),
        'timestamp': round(time.time()*1000)
    }
    sio.send(msg_data, room=sid)
    # sio.emit('message', msg_data, room=sid)
```

#### 使用firecamp.app进行测试

![firecamp](/images/firecamp_sio.png)

