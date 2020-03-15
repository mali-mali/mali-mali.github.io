---
title: Flask用户管理
date: 2020-03-15 11:30
updated: 2020-03-15 11:30
tags:
- python
- flask
- http
---

在[上一篇](https://mali-mali.github.io/2020/03/07/%E4%BD%BF%E7%94%A8Flask%E5%88%9B%E5%BB%BApython%20http%E6%A1%86%E6%9E%B6/) 中，我们使用了Flask创建了一个python的http后台服务，
今天我们来讲讲如何在其中实现用户的登录管理。

## api初始化
首先同样的初始化我们的api
```
from flask import Flask
from flask_login import LoginManager
from flask_migrate import Migrate
from flask_sqlalchemy import SQLAlchemy

db = SQLAlchemy()
login_manager = LoginManager()
login_manager.session_protection = 'strong'


def start_aip(config_name, collection=None):
    """
    初始化API
    :param config_name:
    :param collection:
    :return:
    """
    app = Flask(__name__)

    db.init_app(app)
    Migrate(app, db)
    login_manager.init_app(app)

    from test.my_info import api
    api.init_app(app, version='1.0', title='API', description='A test API')

    return app
```

## 登录验证
这里创建一个User.py
```
import time
import functools
from datetime import datetime
from flask import jsonify, request
from flask_login import UserMixin
from itsdangerous import TimedJSONWebSignatureSerializer as Serializer
from werkzeug.security import generate_password_hash, check_password_hash
from config import settings
from ..models.error import unauthorized, forbidden

from .. import db
from .. import login_manager


class User(UserMixin, db.Model):
    """
    用户表
    :param: UserMixin
    :param: db.Model
    :return:
    """
    __tablename__ = 'users'
    id = db.Column(db.Integer, primary_key=True)
    email = db.Column(db.String(64), unique=True, index=True)
    username = db.Column(db.String(64), unique=True, index=True)
    password_hash = db.Column(db.String(128))
    confirmed = db.Column(db.Boolean, default=False)
    name = db.Column(db.String(64))
    location = db.Column(db.String(64))
    about_me = db.Column(db.Text())
    member_since = db.Column(db.DateTime(), default=datetime.utcnow)
    last_seen = db.Column(db.DateTime(), default=datetime.utcnow)
    avatar_hash = db.Column(db.String(32))
    tokenTime = time.mktime(datetime.now().timetuple())

    def generate_auth_token(self, expiration):
        """
        生成token
        :return:
        """
        s = Serializer(settings.Config.SECRET_KEY, expires_in=expiration)
        token = s.dumps({'id': self.id}).decode('ascii')
        return token

    def verify_password(self, password):
        return check_password_hash(self.password_hash, password)


@login_manager.user_loader
def load_user(user_id):
    return User.query.get(int(user_id))


def login_required(view_func):
    @functools.wraps(view_func)
    def verify_token(*args, **kwargs):
        try:
            # 在请求头上拿到token
            token = request.headers["token"]
        except Exception:
            # 没接收的到token,给前端抛出错误
            return forbidden('缺少参数token')

        s = Serializer(settings.Config.SECRET_KEY)
        try:
            s.loads(token)
        except Exception:
            # print("验证失败, 无效的token")
            return unauthorized('验证失败, 无效的token')

        return view_func(*args, **kwargs)

    return verify_token

```

## 接口及其他
最后我们只需要建立我们的登录接口即可
```
upload_parser = reqparse.RequestParser()
upload_parser.add_argument('token', type=str, location='headers')
user = api.namespace('Login', path='/', description='hello world')
## 应为我们之前使用了swagger在线api这里需要编辑请求时需要传递的参数（可以忽略）
login_model = smart_tax.api.model('Login', {
    'username': fields.String(description='用户名, 默认:admin'),
    'password': fields.String(description='密码, 默认:yiyetong123')
})


@user.route('/login')
class login(Resource):
    """
    登录：
    """
    @user.expect(login_model)
    def post(self):
        '''登录'''
        username = api.payload.get('username', None)
        password = api.payload.get('password', None)
        user = User.query.filter_by(username=username).first()
        if user is not None and user.verify_password(password):
            login_user(user)
            return jsonify({'code': 200, 'token': user.generate_auth_token(tokenExpiration), 'expiration': tokenExpiration})
        else:
            return unauthorized('Invalid username or password.')

    # def get(self):
    #     '''验证失败自动跳转'''
    #     return unauthorized('Unauthorized.')


@user.route('/logout')
class logout(Resource):
    """
    登出
    """
    @login_required
    @user.expect(upload_parser)
    def get(self):
        '''登出'''
        logout_user()
        logoutMessage = {"message": 'You have been logged out.'}
        flash(logoutMessage)
        return jsonify(logoutMessage)
```
>在需要进行登录校验的接口上添加 @login_required 后该接口必须输入登入时返回的token信息才可访问， @user.expect(upload_parser) 为swagger在线api的装饰器。

### 自定义错误信息
```
def bad_request(message):
    """
    服务器不理解客户端的请求，未做任何处理。
    :param message:
    :return:
    """
    return {'code': 400, 'message': message, 'data': None}


def unauthorized(message):
    """
    用户未提供身份验证凭据，或者没有通过身份验证。
    :param message:
    :return:
    """
    return {'code': 401, 'message': message, 'data': None}
    # return plugin.api.abort(code=200, message=message)


def forbidden(message):
    """
    用户通过了身份验证，但是不具有访问资源所需的权限。
    :param message:
    :return:
    """
    return {'code': 403, 'message': message, 'data': None}


def NotFound(message):
    """
    
    :param message:
    :return:
    """
    return {'code': 404, 'message': message, 'data': None}


def NotFoundDevice(message):
    """
    
    :param message:
    :return:
    """
    return {'code': 412, 'message': message, 'data': None}


def internalServerError(message):
    """
    
    :param message:
    :return:
    """
    return {'code': 500, 'message': message, 'data': None}



def deviceTimeoutError(message):
    """
    
    :param message:
    :return:
    """
    return {'code': 501, 'message': message, 'data': None}


def parameterError(message):
    """
    
    :param message:
    :return:
    """
    return {'code': 502, 'message': message, 'data': None}


def successful(data):
    """
    成功
    :param data:
    :return:
    """
    return {'code': 200, 'message': None, 'data': data}
```