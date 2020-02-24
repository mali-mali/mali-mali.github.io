---
title: QtWebEngineWidgets在某些设备上崩溃的问题
date: 2020-02-24 20:10
updated: 2020-02-24 20:10
tags:
- PyQt5
- QtWebEngineWidgets
---

今天遇到一个比较头疼的问题，使用了QtWebEngineWidgets模块开发的程序导出exe文件之后在某些设备上会崩溃的问题。
我碰到的设备具体都是win7的设备。

## 解决方法

这个问题不是很常见，网上答案也不多，大多说的是因为显卡驱动的原因，需要更新显卡驱动。不过我没有试过，具体有没有不知道。

而我使用的方法呢，是来自 [CSND](https://blog.csdn.net/xujun1991/article/details/88122506?depth_1-utm_source=distribute.pc_relevant.none-task&utm_source=distribute.pc_relevant.none-task) 上的文章他的第二点：
```
2.必须把libEGL.dll、libGLESv2.dll、d3dcompiler_47.dll、opengl32sw.dll这些文件拷贝到对应的exe路径下。
```
这里稍微有些不一样的地方，只需要将 程序的运行目录下\PyQt5\Qt\bin 里面的
```
libEGL.dll；
libGLESv2.dll；
d3dcompiler_47.dll；
opengl32sw.dll
```
四个dll拷贝到exe运行路径下就可以正常运行了。