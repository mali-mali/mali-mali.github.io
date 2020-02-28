---
title: PyQt5自定义控件之环形进度条
date: 2020-02-28 20:15
updated: 2020-02-28 20:15
tags:
- PyQt5
- 样式
- 动画
- 自定义控件
---

本文包含了QTimeLine、QPainter模块的运用
使用Qpainter绘制圆环图案，QTimeLine实现填充动画。[参考](https://blog.csdn.net/gongjianbo1992/article/details/103571569)
追加了数字文字的变化动画。

## 代码
```
from PyQt5.Qt import QColor, QRectF, QConicalGradient
from PyQt5.QtCore import Qt, QTimeLine
from PyQt5.QtGui import QPainter, QPainterPath, QFont
from PyQt5.QtWidgets import QLabel


class RingStatistics(QLabel):

    def __init__(self, parent, width, color, ring_color=QColor(0, 0, 0, 0), gradient=None):
        super(RingStatistics, self).__init__(parent)
        # 记录角度
        self.angle = 0
        self.Ptext = '0'
        self.precision = '1'
        # 这个是用于绘制的，angle才是真实的角度
        self.drawAngle = self.angle
        self.drawText = self.Ptext
        # 进度条宽度
        self.lineWidth = width
        # 环形时间轴
        self.ringTime = QTimeLine(1000, self)
        self.ringTime.frameChanged.connect(self.ringTimeline)
        # 文字时间轴
        self.textTime = QTimeLine(1000, self)
        self.textTime.frameChanged.connect(self.TextTimeline)
        # 填充色
        self.color = color
        self.ring_color = ring_color
        # 渐变色
        if gradient:
            self.gradient = gradient
        else:
            self.gradient = self.color

    def ringTimeline(self, frame):
        """环形时间轴"""
        self.drawAngle = frame
        self.update()

    def TextTimeline(self, frame):
        """文字时间轴"""
        self.drawText = format(float(frame) * float(self.precision), '.' + str(len(self.precision.split(".")[1])) + 'f')
        self.update()

    def setAngle(self, value, text):
        """设置值、播放动画"""
        self.drawAngle = self.angle
        self.angle = int(value * 360)
        self.drawText = self.FloatToInt(self.Ptext)
        self.Ptext = self.FloatToInt(float(text))
        self.ringTime.stop()
        self.ringTime.setFrameRange(self.drawAngle, self.angle)
        self.ringTime.start()
        self.textTime.stop()
        self.textTime.setFrameRange(self.drawText, self.Ptext)
        self.textTime.start()

    def setPText(self, p_str='0', size=18, font='微软雅黑', color=QColor(0, 0, 0, 0), unit=''):
        """初始化绘制的文字"""
        self.Ptext = p_str
        self.Psize = size
        self.Pfont = font
        self.Pcolor = color
        self.unit = unit

    def paintEvent(self, event):
        the_rect = QRectF(0, 0, self.width(), self.height())
        if the_rect.isNull():
            return
        # 画笔
        painter = QPainter(self)
        painter.fillRect(the_rect, QColor(0, 0, 0, 0))
        painter.setRenderHints(QPainter.Antialiasing | QPainter.SmoothPixmapTransform, on=True)
        # 镜像翻转，这样就是顺时针
        painter.setViewport(self.width(), 0, -self.width(), self.height())

        # 填充两个圆相交的部分
        the_path = QPainterPath()
        the_path.addEllipse(the_rect.adjusted(1, 1, -1, -1))
        the_path.addEllipse(the_rect.adjusted(
            1 + self.lineWidth, 1 + self.lineWidth, -1 - self.lineWidth, -1 - self.lineWidth))
        painter.fillPath(the_path, self.ring_color)

        # 径向渐变（参数为中心点和起始角度），默认时从右侧开始逆时针算的
        the_gradient = QConicalGradient(the_rect.center(), 90)
        the_angle = self.drawAngle / 360
        the_gradient.setColorAt(0, self.color)
        the_gradient.setColorAt(the_angle, self.gradient)
        if the_angle + 0.001 < 1:
            the_gradient.setColorAt(the_angle + 0.001, QColor(0, 0, 0, 0))
        painter.fillPath(the_path, the_gradient)

        p = QPainter(self)
        p.setRenderHint(QPainter.TextAntialiasing, True)
        p.setPen(self.Pcolor)
        p.setFont(QFont(self.Pfont, self.Psize))
        if '_' in self.unit:
            if self.unit[0] == '_':
                s = str(self.drawText) + self.unit[1:]
            else:
                s = self.unit[:-1] + str(self.drawText)
        else:
            s = str(self.drawText)
        p.drawText(self.rect(), Qt.AlignCenter, s)

    def FloatToInt(self, f):
        """处理浮点数数据"""
        if '.' in str(f):
            self.precision = '0.'
            i = 0
            while i < len(str(f).split(".")[1]):
                if i < len(str(f).split(".")[1]) - 1:
                    self.precision = self.precision + '0'
                else:
                    self.precision = self.precision + '1'
                i += 1
            return int(float(f) / float(self.precision))
        else:
            self.precision = '1'
            return int(f)
```

## 主要方法
```
    def ringTimeline(self, frame):
        """环形时间轴"""
        self.drawAngle = frame
        self.update()

    def TextTimeline(self, frame):
        """文字时间轴"""
        self.drawText = format(float(frame) * float(self.precision), '.' + str(len(self.precision.split(".")[1])) + 'f')
        self.update()

    def setAngle(self, value, text):
        """设置值、播放动画"""
        self.drawAngle = self.angle
        self.angle = int(value * 360)
        self.drawText = self.FloatToInt(self.Ptext)
        self.Ptext = self.FloatToInt(float(text))
        self.ringTime.stop()
        self.ringTime.setFrameRange(self.drawAngle, self.angle)
        self.ringTime.start()
        self.textTime.stop()
        self.textTime.setFrameRange(self.drawText, self.Ptext)
        self.textTime.start()
```
>QTimeLine.setFrameRange() 设置动画起始值，和目标值。这里参数为 int 类型参数。

```
    def paintEvent(self, event):
        the_rect = QRectF(0, 0, self.width(), self.height())
        if the_rect.isNull():
            return
        # 画笔
        painter = QPainter(self)
        painter.fillRect(the_rect, QColor(0, 0, 0, 0))
        painter.setRenderHints(QPainter.Antialiasing | QPainter.SmoothPixmapTransform, on=True)
        # 镜像翻转，这样就是顺时针
        painter.setViewport(self.width(), 0, -self.width(), self.height())

        # 填充两个圆相交的部分
        the_path = QPainterPath()
        the_path.addEllipse(the_rect.adjusted(1, 1, -1, -1))
        the_path.addEllipse(the_rect.adjusted(
            1 + self.lineWidth, 1 + self.lineWidth, -1 - self.lineWidth, -1 - self.lineWidth))
        painter.fillPath(the_path, self.ring_color)

        # 径向渐变（参数为中心点和起始角度），默认时从右侧开始逆时针算的
        the_gradient = QConicalGradient(the_rect.center(), 90)
        the_angle = self.drawAngle / 360
        the_gradient.setColorAt(0, self.color)
        the_gradient.setColorAt(the_angle, self.gradient)
        if the_angle + 0.001 < 1:
            the_gradient.setColorAt(the_angle + 0.001, QColor(0, 0, 0, 0))
        painter.fillPath(the_path, the_gradient)

		# 绘制文字
        p = QPainter(self)
        p.setRenderHint(QPainter.TextAntialiasing, True)
        p.setPen(self.Pcolor)
        p.setFont(QFont(self.Pfont, self.Psize))
        if '_' in self.unit:
            if self.unit[0] == '_':
                s = str(self.drawText) + self.unit[1:]
            else:
                s = self.unit[:-1] + str(self.drawText)
        else:
            s = str(self.drawText)
        p.drawText(self.rect(), Qt.AlignCenter, s)
```


## 补充

### QPainterPath:
```
QPainterPath.addEllipse  # 绘制椭圆形
QPainterPath.addPolygon  # 多边形
QPainterPath.addRect     # 矩形
QPainterPath.addText     # 文字
QPainterPath.arcTo       # 弧形
QPainterPath.cubicTo     # 贝塞尔曲线
```

### QRectF
```
QRectF.adjusted  # 四个端点的坐标偏移
```
