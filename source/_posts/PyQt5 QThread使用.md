---
title: PyQt5 QThread使用
date: 2020-03-29 10:10
updated: 2020-03-29 10:10
tags:
- python
- PyQt5
- QThread
---

在使用PyQt进行开发时，我们常常会使用线程来将部分逻辑代码与界面主线程分离。
这时我们一般会使用Python的threading模块来实现，但是这个模块在某些情况下会导致UI界面卡死（可能是我使用方法不对），所以我更推荐使用QThread来进行多线程操作。

## 代码示例
这里我们创建一个继承至QThread的类。
```
class myThread(QThread):

    pyqtsignal = pyqtSignal()

    def __init__(self):
        super(myThread, self).__init__()
        """初始化线程"""

    def run(self):
        try:
            """这里是我们要进行的耗时操作"""
			
			self.pyqtsignal.emit()  # 发送信号
			
            self.exit(0)  # 关闭线程

        except Exception as e:
            print(e)
            self.exit(0)
```

>这里要注意QThread中的信号只能通过pyqtSignal信号槽的方式来传递。

调用很简单
```
myt = myThread()
myt.pyqtsignal.connect(...)
myt.start()
```