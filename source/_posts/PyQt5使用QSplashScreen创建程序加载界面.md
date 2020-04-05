---
title: PyQt5使用QSplashScreen创建程序加载界面
date: 2020-04-05 10:00
updated: 2020-04-05 10:00
tags:
- PyQt5
- QSplashScreen
---

由于Qt程序往往启动较慢，所以我们就需要一个启动界面来做缓冲。（没什么用的样子）

## 代码案例
首先我们创建一个继承至QSplashScreen的类
```
import time
from PyQt5.QtCore import Qt
from PyQt5.QtGui import QFont, QPixmap
from PyQt5.QtWidgets import QSplashScreen


class SplashPanel(QSplashScreen):
    def __init__(self):
        super(SplashPanel, self).__init__()
        message_font = QFont('Microsoft YaHei')
        message_font.setBold(True)
        message_font.setPointSize(12)
        self.setFont(message_font)
        pixmap = QPixmap('image/load.png')
        self.setPixmap(pixmap)
        self.show()
        for i in range(1, 5):
            self.showMessage('正在加载文件资源{}'.format('.' * i), alignment=Qt.AlignBottom, color=Qt.white)
            time.sleep(0.15)
    def mousePressEvent(self, evt):
        pass
        # 重写鼠标点击事件，阻止点击后消失
    def mouseDoubleClickEvent(self, *args, **kwargs):
        pass
        # 重写鼠标移动事件，阻止出现卡顿现象
    def enterEvent(self, *args, **kwargs):
        pass
        # 重写鼠标移动事件，阻止出现卡顿现象
    def mouseMoveEvent(self, *args, **kwargs):
        pass
        # 重写鼠标移动事件，阻止出现卡顿现象

```

然后是我们的启动函数
```
if __name__ == "__main__":
	app = QApplication(sys.argv)
	splash = SplashPanel()
	app.processEvents()  # 不阻塞程序
	
	myWin = my_win()
	splash.finish(myWin)
	splash.deleteLater()
	myWin.show()
	sys.exit(win.exec_())
```
