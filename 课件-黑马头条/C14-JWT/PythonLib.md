# JWT的Python库

### 独立的JWT Python库

- itsdangerous

    - JSONWebSignatureSerializer
    - TimedJSONWebSignatureSerializer  （可设置有效期）

- pyjwt

    <https://pyjwt.readthedocs.io/en/latest/>

    ### 安装

    ```shell
    $ pip install pyjwt
    ```

    ### 用例

    ```python
    >>> import jwt
    
    >>> encoded_jwt = jwt.encode({'some': 'payload'}, 'secret', algorithm='HS256')
    >>> encoded_jwt
    'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzb21lIjoicGF5bG9hZCJ9.4twFt5NiznN84AWoo1d7KO1T_yoc0Z6XOpOVswacPZg'
    
    >>> jwt.decode(encoded_jwt, 'secret', algorithms=['HS256'])
    {'some': 'payload'}
    ```

### 头条项目封装

```python
import jwt
from flask import current_app


def generate_jwt(payload, expiry, secret=None):
    """
    生成jwt
    :param payload: dict 载荷
    :param expiry: datetime 有效期
    :param secret: 密钥
    :return: jwt
    """
    _payload = {'exp': expiry}
    _payload.update(payload)

    if not secret:
        secret = current_app.config['JWT_SECRET']

    token = jwt.encode(_payload, secret, algorithm='HS256')
    return token.decode()


def verify_jwt(token, secret=None):
    """
    检验jwt
    :param token: jwt
    :param secret: 密钥
    :return: dict: payload
    """
    if not secret:
        secret = current_app.config['JWT_SECRET']

    try:
        payload = jwt.decode(token, secret, algorithm=['HS256'])
    except jwt.PyJWTError:
        payload = None

    return payload

```



