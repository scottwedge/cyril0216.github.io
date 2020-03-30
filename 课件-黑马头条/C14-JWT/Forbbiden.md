# JWT禁用问题

### 需求

token颁发给用户后，在有效期内服务端都会认可，但是如果在token的有效期内需要让token失效，该怎么办？

此问题的应用场景：

- 用户修改密码，需要颁发新的token，禁用还在有效期内的老token
- 后台封禁用户

#### 解决方案

在redis中使用set类型保存新生成的token

```python
key = 'user:{}:token'.format(user_id)
pl = redis_client.pipeline()
pl.sadd(key, new_token)
pl.expire(key, token有效期)
pl.execute()
```

| 键                   | 类型 | 值      |
| -------------------- | ---- | ------- |
| user:{user_id}:token | set  | 新token |

客户端使用token进行请求时，如果验证token通过，则从redis中判断是否存在该用户的user:{}:token记录：

- 若不存在记录，放行，进入视图进行业务处理
- 若存在，则对比本次请求的token是否在redis保存的set中：
    - 若存在，则放行
    - 若不在set的数值中，则返回403状态码，不再处理业务逻辑

```python
key = 'user:{}:token'.format(user_id)
valid_tokens = redis_client.smembers(key, token)
if valid_tokens and token not in valid_tokens:
  return {'message': 'Invalid token'.}, 403
```

##### 说明：

1. redis记录设置有效期的时长是一个token的有效期，保证旧token过期后，redis的记录也能自动清除，不占用空间。
2. 使用set保存新token的原因是，考虑到用户可能在旧token的有效期内，在其他多个设备进行了登录，需要生成多个新token，这些新token都要保存下来，既保证新token都能正常登录，又能保证旧token被禁用

