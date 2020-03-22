---
title: 获取QtWebEngine的Cookie信息
date: 2020-03-22 10:30
updated: 2020-03-22 10:30
tags:
- python
- QtWebEngine
---

[之前](https://mali-mali.github.io/2020/03/02/%E4%BD%BF%E7%94%A8QtWebEngineWidgets%E5%BC%80%E5%8F%91Web%E6%B5%8F%E8%A7%88%E5%99%A8/)我们使用QtWebEngine创建了一个浏览器，并且为这个浏览器添加了cookie信息，那么我们有没有可能需要的是浏览器的cookie信息呢？

## 获取QtWebEngine浏览器Cookie信息
在构造函数中添加如下代码
```
	...
    def __init__(self, parent=None):
        super().__init__(parent)
		...
        QWebEngineProfile.defaultProfile().cookieStore().cookieAdded.connect(self.onCookieAdd)
        self.cookies = {}  # 存放cookie字典
	...
```

接下来添加两个函数
```
    def onCookieAdd(self, cookie):  # 处理cookie添加的事件
        print(cookie)
        name = cookie.name().data().decode('utf-8')  # 先获取cookie的名字，再把编码处理一下
        value = cookie.value().data().decode('utf-8')  # 先获取cookie值，再把编码处理一下
        self.cookies[name] = value  # 将cookie保存到字典里

    # 获取cookie
    def get_cookie(self):
        print(self.cookies.items())
        # cookie_str = ''
        # for key, value in self.cookies.items():  # 遍历字典
        #     cookie_str += (key + '=' + value + ';')  # 将键值对拿出来拼接一下
        return dict(self.cookies.items())  # 返回拼接好的字符串
```

这时我们会获取的一些不需要的cookie信息，提取一下
```
cookie = 'JSESSIONID=' + self.get_cookie().get('JSESSIONID', '')
```