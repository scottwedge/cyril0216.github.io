# 头条项目实施方案

### 需求

设置有效期，但有效期不宜过长，需要刷新。

如何解决刷新问题？

- 手机号+验证码（或帐号+密码）验证后颁发接口调用token与refresh_token（刷新token）

- Token 有效期为2小时，在调用接口时携带，每2小时刷新一次 

- 提供refresh_token，refresh_token 有效期14天 

- 在接口调用token过期后凭借refresh_token 获取新token

- 未携带token 、错误的token或接口调用token过期，返回401状态码

- refresh_token 过期返回403状态码，前端在使用refresh_token请求新token时遇到403状态码则进入用户登录界面从新认证。

- token的携带方式是在请求头中使用如下格式：

    ```http
    Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJzb21lIjoicGF5bG9hZCJ9.4twFt5NiznN84AWoo1d7KO1T_yoc0Z6XOpOVswacPZg
    ```

    注意：Bearer前缀与token中间有一个空格

#### 实现

##### 注册或登录获取token

toutiao/resources/user/passport.pty

```python
class AuthorizationResource(Resource):
    """
    认证
    """
    def _generate_tokens(self, user_id, with_refresh_token=True):
        """
        生成token 和refresh_token
        :param user_id: 用户id
        :return: token, refresh_token
        """
         # 颁发JWT
        now = datetime.utcnow()
        expiry = now + timedelta(hours=current_app.config['JWT_EXPIRY_HOURS'])
        token = generate_jwt({'user_id': user_id, 'refresh': False}, expiry)
        refresh_token = None
        if with_refresh_token:
            refresh_expiry = now + timedelta(days=current_app.config['JWT_REFRESH_DAYS'])
            refresh_token = generate_jwt({'user_id': user_id, 'refresh': True}, refresh_expiry)
        return token, refresh_token

    def post(self):
        """
        登录创建token
        """
        json_parser = RequestParser()
        json_parser.add_argument('mobile', type=parser.mobile, required=True, location='json')
        json_parser.add_argument('code', type=parser.regex(r'^\d{6}$'), required=True, location='json')
        args = json_parser.parse_args()
        mobile = args.mobile
        code = args.code

        # 从redis中获取验证码
        key = 'app:code:{}'.format(mobile)
        try:
            real_code = current_app.redis_master.get(key)
        except ConnectionError as e:
            current_app.logger.error(e)
            real_code = current_app.redis_slave.get(key)

        try:
            current_app.redis_master.delete(key)
        except ConnectionError as e:
            current_app.logger.error(e)

        if not real_code or real_code.decode() != code:
            return {'message': 'Invalid code.'}, 400

        # 查询或保存用户
        user = User.query.filter_by(mobile=mobile).first()

        if user is None:
            # 用户不存在，注册用户
            user_id = current_app.id_worker.get_id()
            user = User(id=user_id, mobile=mobile, name=mobile, last_login=datetime.now())
            db.session.add(user)
            profile = UserProfile(id=user.id)
            db.session.add(profile)
            db.session.commit()
        else:
            if user.status == User.STATUS.DISABLE:
                return {'message': 'Invalid user.'}, 403

        token, refresh_token = self._generate_tokens(user.id)

        return {'token': token, 'refresh_token': refresh_token}, 201

```

##### 请求钩子

common/utils/middlewares.py

```python
from flask import request, g
from .jwt_util import verify_jwt

def jwt_authentication():
    """
    根据jwt验证用户身份
    """
    g.user_id = None
    g.is_refresh_token = False
    authorization = request.headers.get('Authorization')
    if authorization and authorization.startswith('Bearer '):
        token = authorization.strip()[7:]
        payload = verify_jwt(token)
        if payload:
            g.user_id = payload.get('user_id')
            g.is_refresh_token = payload.get('refresh')

```

##### 强制登录装饰器

common/utils/decorators.py

```python
def login_required(func):
    """
    用户必须登录装饰器
    使用方法：放在method_decorators中
    """
    @wraps(func)
    def wrapper(*args, **kwargs):
        if not g.user_id:
            return {'message': 'User must be authorized.'}, 401
        elif g.is_refresh_token:
            return {'message': 'Do not use refresh token.'}, 403
        else:
            return func(*args, **kwargs)

    return wrapper
```

##### 更新token接口

toutiao/resources/user/passport.py

```python
class AuthorizationResource(Resource):
    """
    认证
    """
    
    ...
    
    # 补充put方式 更新token接口
    def put(self):
        """
        刷新token
        """
        user_id = g.user_id
        if user_id and g.is_refresh_token:
            token, refresh_token = self._generate_tokens(user_id, with_refresh_token=False)
            return {'token': token}, 201
        else:
            return {'message': 'Wrong refresh token.'}, 403
```



