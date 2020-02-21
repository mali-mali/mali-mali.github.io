---
title: 常用的PyQt5控件样式表
date: 2020-02-21 12:09
updated: 2020-02-21 12:10
tags:
- PyQt5
- 样式
---

在最近的工作中大量的使用了PyQt的.setStyleSheet方法来对控件的样式进行自定义，或者制作自己的控件

## 常用样式

```
背景类：
	background:rgb(245,254,254,1);
	background-image:url(.../xxx.png);
	border-image:url(.../xxx.png);
```
>注意：这里 background-image 和 border-image 都是设置背景图片，区别在于 border-image 图片可以自动适应控件的大小，而 background-image 不行。


```
文字类：
	font-size:14px;  # 字号
    font-family:Microsoft YaHei;  # 字体
    font-weight:400;"  # 笔重
    color:rgba(30,159,255,1);  # 颜色
    line-height:40px;  # 行距
```

```
边框类：
	border:none;  # 无边框
	border:1px solid rgb(255, 255, 255, 1);  # 全边框
	border-top:1px solid rgb(255, 255, 255, 1);  # 上边框
	border-right:1px solid rgb(255, 255, 255, 1);  # 右边框
	border-left:1px solid rgb(255, 255, 255, 1);  # 左边框
	border-bottom:1px solid rgb(255, 255, 255, 1);  # 下边框
	border-radius:60%;  # 圆角，也可以使用像素做参数
	
	padding-top:1px;  # 边距
	padding-right:1px;
	padding-left:1px;
	padding-bottom:1px;
	
	opacity:0.9;  # 透明度
```

## 控件设置

### 文本
```
QLabel{font-size:14px;
	   font-family:Microsoft YaHei;
	   font-weight:400;
	   color:rgba(51,51,51,1);
	   line-height:40px}
```

### 按钮
```
QPushButton{background:rgb(255,255,255,1);
			border:none;
			border-radius:25%}

# 鼠标悬停样式			
QPushButton:hover{background:rgb(30,159,255,1)}

# 选中样式
QPushButton:disabled{border-image: url(.../被选中.png)}
```

### 下拉框
```
QComboBox{background:rgba(245,245,245,1);
		  font-size:14px;
		  font-family:Microsoft YaHei;
		  font-weight:400;
		  color:rgba(51,51,51,1);
		  line-height:1px;
		  border:1px solid rgba(179,179,179,1);
		  border-radius:2px;
		  padding-left:18px}
		  
# 下拉按钮		  
QComboBox::drop-down{width: 52px;
					 border: none}
					 
# 下拉按钮图标					 
QComboBox::down-arrow {border-image: url(.../向下.png);
					   height:16px;
					   width:12px}
```

### 输入框
```
QLineEdit{background:rgba(245,245,245,1);
		  border:1px solid rgba(30,159,255,1);
		  opacity:0.9;
	   	  border-radius:2px;
		  font-size:14px;
		  font-family:Microsoft YaHei;
		  font-weight:400;
		  color:rgba(134,134,134,1);
		  line-height:72px;
		  padding-left:15px}
```

>本文用于记录我在工作中常用的控件样式表，后续还会继续更新。