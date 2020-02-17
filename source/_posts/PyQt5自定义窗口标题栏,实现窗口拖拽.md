---
title: PyQt5 自定义窗口标题栏, 实现窗口拖拽
date: 2020-02-13 16:00:00
updated: 2020-02-13 16:00:00
tags: 
- PyQt5 应用
- 自定义标题栏
---

最近在使用pyqt5做窗口应用程序，因为原始的标题栏不能满足需求我们常常会使用到自定义的标题栏。
本篇放上一个最简单的例子提供参考

## 隐藏原始标题栏
```
class test(QMainWindow):

    def __init__(self):
        super(test, self).__init__(None, Qt.FramelessWindowHint)  # 无边框设置
		self.setFixedSize(880, 640)
        self.setStyleSheet("QMainWindow{background:rgba(255,255,255,255)}")
		self.init()
```
>使用 Qt.FramelessWindowHint将我们的窗口设置为无边框窗口


## 创建一个标题栏
```
    def init(self):
        # 创建一个标题栏
        self.titlebar = QLabel(self)
        self.titlebar.setFixedSize(880, 40)
        self.titlebar.move(0, 0)
        self.titlebar.setStyleSheet("QLabel{background:rgba(245,245,245,255)}")

        # 图标
        self.icon = QLabel(self)
        self.icon.setFixedSize(20, 20)
        self.icon.move(10, 10)
        self.icon.setStyleSheet("QLabel{background-image: url(image/main_win/icon.png)}")

        # 标题
        self.title = QLabel(self)
        self.title.setText('我的自定义标题栏')
        self.title.move(37, 6)
        self.title.setStyleSheet("QLabel{font-size:14px;"
                                 "font-family:Microsoft YaHei;"
                                 "font-weight:400;"
                                 "color:rgba(51,51,51,1);"
                                 "line-height:1px;")

        # 关闭按钮
        self.close_btn = QPushButton(self)
        self.close_btn.setFixedSize(16, 16)
        self.close_btn.move(842, 18)
        self.close_btn.setStyleSheet("QPushButton{background-image: url(image/main_win/close.png);"
                                     "border:none}")
        self.close_btn.pressed.connect(self.close)

        # # 最大化按钮
        # self.max_btn = QPushButton(self)
        # self.max_btn.setFixedSize(16, 16)
        # self.max_btn.move(792, 18)
        # self.max_btn.setStyleSheet("QPushButton{background-image: url(image/main_win/min.png);"
        #                            "border:none}")
        # self.max_btn.pressed.connect(self.ShowRestoreWindow)

        # 最小化按钮
        self.min_btn = QPushButton(self)
        self.min_btn.setFixedSize(16, 16)
        self.min_btn.move(742, 18)
        self.min_btn.setStyleSheet("QPushButton{background-image: url(image/main_win/min.png);"
                                   "border:none}")
        self.min_btn.pressed.connect(self.showMinimized)

    # def ShowRestoreWindow(self):
    #     """窗口最大化以及恢复"""
    #     if self.isMaximized():
    #         self.showNormal()
    #     else:
    #         self.showMaximized()
    #     self.titleSize()

    def mousePressEvent(self, QMouseEvent):
        """鼠标点击事件"""
        self.isPressed = True
        self.startMovePos = QMouseEvent.globalPos()

    def mouseMoveEvent(self, QMouseEvent):
        """鼠标拖拽事件"""
        if self.isPressed:
            movePoint = QMouseEvent.globalPos() - self.startMovePos
            widgetPos = self.pos()
            self.startMovePos = QMouseEvent.globalPos()
            self.move(widgetPos.x() + movePoint.x(), widgetPos.y() + movePoint.y())

    def mouseReleaseEvent(self, QMouseEvent):
        """鼠标放开事件"""
        self.isPressed = False
```
>这里我注释掉了最大化窗口事件，因为没有做好控件的自适应方案。