# 头条在线消息推送实现

### 需求

在头条的Flask应用中，用户关注后需要推送消息，通过消息队列告知IM服务为用户进行推送

![push](/images/推送关注通知流程.png)

在Socket.IO 框架中可以选择使用以下两种方式作为消息中间件：

- 使用Redis

    ```python
    mgr = socketio.RedisManager('redis://')
    sio = socketio.Server(client_manager=mgr)
    ```

- 使用RabbitMQ

    ```shell
    pip install kombu
    ```

    ```python
    mgr = socketio.KombuManager('amqp://')
    sio = socketio.Server(client_manager=mgr)
    ```

### 实现

因为要给指定的用户推送消息，所以需要用到用户的身份，用户在客户端携带JWT连接SocketIO服务器，我们在服务器端对jwt token进行验证，对于验证出用户身份(user_id)的客户端，将其添加到名为用户id的room房间中，方便按照user_id进行推送。

#### socketio服务端编写

在toutiao-backend/im/main.py中补充添加搜寻包的路径，方便使用utils中的jwt_utils模块

```python
import eventlet
eventlet.monkey_patch()

import eventlet.wsgi
import sys
import os

# 补充搜索包路径
BASE_DIR = os.path.dirname(os.path.dirname(os.path.abspath(__file__)))
sys.path.insert(0, os.path.join(BASE_DIR, 'common'))

if len(sys.argv) < 2:
    print('Usage: python main.py [port]')
    exit(1)

port = int(sys.argv[1])

from server import app
import chat
import notify

SERVER_ADDRESS = ('', port)
sock = eventlet.listen(SERVER_ADDRESS)
eventlet.wsgi.server(sock, app)
```

在toutiao-backend/im/server.py文件中补充消息队列rabbitmq的配置信息和jwt使用的秘钥

```python
import socketio

RABBITMQ = 'amqp://python:rabbitmqpwd@localhost:5672/toutiao'
JWT_SECRET = 'TPmi4aLWRbyVq8zu9v82dWYW17/z+UvRnYTt4P6fAXA'

mgr = socketio.KombuManager(RABBITMQ)

sio = socketio.Server(async_mode='eventlet', client_manager=mgr)
app = socketio.Middleware(sio)
```

在toutiao-backend/im目录中新建notify.py

```python
from server import sio, JWT_SECRET
from werkzeug.wrappers import Request
from utils.jwt_util import verify_jwt

def check_jwt_token(environ):
    """
    检验jwt token
    :param environ:
    :return:
    """
    request = Request(environ)
    token = request.args.get('token')
    if token:
        payload = verify_jwt(token, JWT_SECRET)
        if payload:
            user_id = payload.get('user_id')
            return user_id
    return None

@sio.on('connect')
def on_connect(sid, environ):
    """
    与客户端建立连接后执行
    """
    # 检验连接客户端的jwt token
    user_id = check_jwt_token(environ)
    print('user_id={}'.format(user_id))
    # 若检验出user_id，将此客户端添加到user_id的room中
    if user_id:
        sio.enter_room(sid, str(user_id))

@sio.on('disconnect')
def on_disconnect(sid):
    """
    与客户端断开连接时执行
    """
    # 客户端离线时将客户端从所有房间中移除
    rooms = sio.rooms(sid)
    for room in rooms:
        sio.leave_room(sid, room)
```

#### flask web服务端编写

在`toutiao-backend/toutiao/__init__.py`中 添加sio对象的创建

```python
import socketio

def create_app(config, enable_config_file=False):
    """
    创建应用
    :param config: 配置信息对象
    :param enable_config_file: 是否允许运行环境中的配置文件覆盖已加载的配置信息
    :return: 应用
    """
	...
    
    # socket.io
    app.sio = socketio.KombuManager(app.config['RABBITMQ'], write_only=True)

	...
```

在toutiao-backend/toutiao/resources/user/following.py 用户关注接口视图中添加发送事件消息

```python
class FollowingListResource(Resource):
    """
    关注用户
    """
    method_decorators = {
        'post': [login_required],
        'get': [login_required],
    }

    def post(self):
        """
        关注用户
        """
        # 关注用户的数据库保存
        ...
        
        # 发送关注通知
        _user = cache_user.UserProfileCache(g.user_id).get()
        _data = {
            'user_id': g.user_id,
            'user_name': _user['name'],
            'user_photo': _user['photo'],
            'timestamp': int(time.time())
        }
        current_app.sio.emit('following notify', data=_data, room=str(target))

        return {'target': target}, 201

```

#### 效果

![关注推送效果](/images/用户关注推送.png)