---
title: 使用QtWebEngineWidgets开发Web浏览器
date: 2020-03-02 22:00
updated: 2020-03-02 22:00
tags:
- PyQt5
- QtWebEngineWidgets
- Web浏览器
---

由于公司的项目常常使用混编模式开发，所以在客户端软件中嵌套web页面就成了必不可少的要求。
此次我使用到了PyQt5开发客户端界面，并尝试将web页面嵌套进客户端窗口内。
具体使用的Qt模块为QtWebEngineWidgets模块，该模块是在python里是需要单独安装和导入的。

## 代码案例
```
import sys
from PyQt5.Qt import QApplication
from PyQt5.QtCore import QUrl
from PyQt5.QtWidgets import QWidget
from PyQt5.QtWebEngineWidgets import QWebEngineView


class my_Browser(QWidget):

    def __init__(self):
        super(my_Browser, self).__init__(None)
        self.setFixedSize(1024, 768)

        self.browser = QWebEngineView(self)  # web窗口
        self.browserInit()  # 生成浏览器控件

        self.openUrl('https://www.baidu.com/')
        # self.openUrl('/百度一下，你就知道.mhtml')

    def browserInit(self):
        """创建一个浏览器"""

        self.browser.setFixedSize(1024, 768)

    def openUrl(self, url):
        """打开网页"""
        self.browser.load(QUrl(url))
        # self.browser.load(QUrl.fromLocalFile(url))  # 打开本地路径的网页


if __name__ == "__main__":
    win = QApplication(sys.argv)
    myWin = my_Browser()
    myWin.show()
    sys.exit(win.exec_())
```
>注意使用网页链接时需要完整的网页地址https和http是必须的；打开本地web文件只需要带人正确的路径即可，我这里直接放在了同一个目录下，百度的页面为.mhtml，当然.html肯定是可以的。

上面为一个简单的案例，这里我在补充一个简单的知识的，开我们的开发中通常不会只是打开一个web页面这么简单，我们还会需要登录后台并实现web页面的自动登录，这里就需要用到cookie了。

## 为url添加cookie信息
```
from PyQt5.QtWebEngineWidgets import QWebEngineView, QWebEngineProfile
from PyQt5.QtNetwork import QNetworkCookie

	...

    def __init__(self):
        super(my_Browser, self).__init__(None)
        self.setFixedSize(1024, 768)
        self.session = 'FSDF45643216546465DSD56464'  # 这里随便瞎写一个，当然我们可以将其作为参数带入
	...

    def openUrl(self, url):
        """打开网页"""
        self.my_cookie = QNetworkCookie()
        self.my_cookie.setName('JSESSIONID'.encode())
        self.my_cookie.setValue(self.session.encode())
        self.my_cookie.setPath('/')
        QWebEngineProfile.defaultProfile().cookieStore().deleteAllCookies()
        QWebEngineProfile.defaultProfile().cookieStore().setCookie(self.my_cookie, QUrl(url))
        self.browser.load(QUrl(url))
        # self.browser.load(QUrl.fromLocalFile(url))  # 打开本地路径的网页
		
	...
```
>QNetworkCookie.setPath('/')这里为cookie添加路径信息，应为web开发人员可能会将接口文件和页面文件分开放置。

关于使用QtWebEngineWidgets嵌套web页面还有很多知识，我会在之后的使用中在一一记录。