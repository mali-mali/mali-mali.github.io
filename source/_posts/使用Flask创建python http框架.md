---
title: 使用Flask创建python http框架
date: 2020-03-07 13:00
updated: 2020-03-07 13:00
tags:
- python
- flask
- http
---


在python中，我们可以很方便的通过flask这个框架来创建一个http的后台服务。还可以通过flask_restplus快速创建一个swagger在线api文档。

## http服务创建
先来一个简单的 hello world
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-

from flask import Flask

app = Flask(__name__)


@app.route('/hello')
def helloworld():
    return 'hello world'


if __name__ == "__main__":
    app.run(host='127.0.0.1', port=7777, debug=False, threaded=True)
```

## 生成swagger在线api文档
通常我们都会使用Postman来进行接口的测试，但是多人项目中Postman就有些不方便了。
为了方便接口的测试，我们来创建一个swagger在线api文档
### __init__.py
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from flask_restplus import Api, reqparse

api = Api()

hello = api.namespace('Hello', path='/', description='hello world')

from . import info
```
>新建一个存放接口代码的文件夹my_info，在__init__.py中输入上面的代码，再在这个目录下创建一个info.py用于存放接口代码。

### info.py
```
#!/usr/bin/env python
# -*- coding: utf-8 -*-

from ..my_info import api, hello
from flask_restplus import Resource
from flask_restplus import fields

hello_model = api.model('hello', {
    'text': fields.String(description='测试文字')
})


@hello.route('/hello')
class helloworld(Resource):

    def get(self):
        return 'hello world'

    @hello.expect(hello_model)
    def post(self):
        return api.payload.get('text', '没有发送内容!')
```
再对初始化服务代码进行一些修改

```
#!/usr/bin/env python
# -*- coding: utf-8 -*-
from flask import Flask


def start_aip():
    """
    初始化API
    :return:
    """
    app = Flask(__name__)

    from test.my_info import api
    api.init_app(app, version='1.0', title='API', description='A test API')

    return app


if __name__ == "__main__":
    app = start_aip()
    app.run(host='127.0.0.1', port=7777, debug=False, threaded=True)
```

这样，我们就开启了一个简单的http服务，并且创建了一个 hello world 接口，以及一个在线api文档。