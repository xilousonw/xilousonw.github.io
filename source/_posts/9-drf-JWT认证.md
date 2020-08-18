---
title: 9-drf-JWT认证
date: 2018-05-30 17:53:28
tags:
---
# 一 JWT认证



## 1.1 工作原理

```python
"""
1) jwt = base64(头部).base(载荷).hash256(base64(头部).base(载荷).密钥)
2) base64是可逆的算法、hash256是不可逆的算法
3) 密钥是固定的字符串，保存在服务器
"""
```

## 1.2 drf-jwt

##### 官网

```python
http://getblimp.github.io/django-rest-framework-jwt/
```

##### 安装子：虚拟环境

```python
pip install djangorestframework-jwt
```

##### 使用：user/urls.py

```python
from django.urls import path
from rest_framework_jwt.views import obtain_jwt_token
urlpatterns = [
    path('login/', obtain_jwt_token),
]
```

##### 测试接口：post请求

```python
"""
postman发生post请求

接口：http://api.luffy.cn:8000/user/login/

数据：
{
	"username":"admin",
	"password":"admin"
}
"""
```

## 1.3 drf-jwt开发

##### 配置信息：JWT_AUTH到dev.py中

```python
import datetime
JWT_AUTH = {
    # 过期时间
    'JWT_EXPIRATION_DELTA': datetime.timedelta(days=1),
    # 自定义认证结果：见下方序列化user和自定义response
    'JWT_RESPONSE_PAYLOAD_HANDLER': 'user.utils.jwt_response_payload_handler',  
}
```

##### 序列化user：user/serializers.py(自己创建)

```python
from rest_framework import serializers
from . import models
class UserModelSerializers(serializers.ModelSerializer):
    class Meta:
        model = models.User
        fields = ['username']
```

##### 自定义response：user/utils.py

```python
from .serializers import UserModelSerializers
def jwt_response_payload_handler(token, user=None, request=None):
    return {
        'status': 0,
        'msg': 'ok',
        'data': {
            'token': token,
            'user': UserModelSerializers(user).data
        }
    }
```

##### 基于drf-jwt的全局认证：user/authentications.py(自己创建)

```python
import jwt
from rest_framework.exceptions import AuthenticationFailed
from rest_framework_jwt.authentication import jwt_decode_handler
from rest_framework_jwt.authentication import get_authorization_header
from rest_framework_jwt.authentication import BaseJSONWebTokenAuthentication

class JSONWebTokenAuthentication(BaseJSONWebTokenAuthentication):
    def authenticate(self, request):
        jwt_value = get_authorization_header(request)

        if not jwt_value:
            raise AuthenticationFailed('Authorization 字段是必须的')
        try:
            payload = jwt_decode_handler(jwt_value)
        except jwt.ExpiredSignature:
            raise AuthenticationFailed('签名过期')
        except jwt.InvalidTokenError:
            raise AuthenticationFailed('非法用户')
        user = self.authenticate_credentials(payload)

        return user, jwt_value
```

##### 全局启用：settings/dev.py

```python
REST_FRAMEWORK = {
    # 认证模块
    'DEFAULT_AUTHENTICATION_CLASSES': (
        'user.authentications.JSONWebTokenAuthentication',
    ),
}
```

##### 局部启用禁用：任何一个cbv类首行

```python
# 局部禁用
authentication_classes = []

# 局部启用
from user.authentications import JSONWebTokenAuthentication
authentication_classes = [JSONWebTokenAuthentication]
```

##### 多方式登录：user/utils.py

```python
import re
from .models import User
from django.contrib.auth.backends import ModelBackend
class JWTModelBackend(ModelBackend):
    def authenticate(self, request, username=None, password=None, **kwargs):
        try:
            if re.match(r'^1[3-9]\d{9}$', username):
                user = User.objects.get(mobile=username)
            else:
                user = User.objects.get(username=username)
        except User.DoesNotExist:
            return None
        if user.check_password(password) and self.user_can_authenticate(user):
            return user
```

##### 配置多方式登录：settings/dev.py

```python
AUTHENTICATION_BACKENDS = ['user.utils.JWTModelBackend']
```

##### 手动签发JWT：了解 - 可以拥有原生登录基于Model类user对象签发JWT

```python
from rest_framework_jwt.settings import api_settings

jwt_payload_handler = api_settings.JWT_PAYLOAD_HANDLER
jwt_encode_handler = api_settings.JWT_ENCODE_HANDLER

payload = jwt_payload_handler(user)
token = jwt_encode_handler(payload)
```
