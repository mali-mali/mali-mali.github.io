---
title: PyQt5 鼠标监听事件
date: 2020-04-12 10:30
updated: 2020-04-12 10:30
tags:
- PyQt5
- 鼠标监听
---

在PyQt的开发中常常会使用到鼠标监听，因此做一个记录。

## 代码
```
	def mousePressEvent(self, evt):
        """鼠标点击事件"""


    def mouseDoubleClickEvent(self, *args, **kwargs):
        """鼠标双击事件"""


    def enterEvent(self, *args, **kwargs):
        """鼠标进入事件"""
		
		
	def leaveEvent(self, *args, **kwargs):
        """鼠标移除事件"""


    def mouseMoveEvent(self, *args, **kwargs):
        """鼠标移动事件"""

```
