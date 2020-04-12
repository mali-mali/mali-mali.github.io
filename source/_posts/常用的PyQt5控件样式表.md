---
title: 常用的PyQt5控件样式表
date: 2020-02-21 12:09
updated: 2020-04-12 10:40
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
	image:url();
```
>注意：这里 background-image 和 border-image 都是设置背景图片，区别在于 border-image 图片可以自动适应控件的大小，而 background-image 不行。


```
文字类：
	font-size:14px;  # 字号
    font-family:Microsoft YaHei;  # 字体
    font-weight:400;  # 笔重
    color:rgba(30,159,255,1);  # 颜色
    line-height:40px;  # 行距
	
	文本对齐
	text-align: left;
	text-align: right;
	text-align: top;
	text-align: bottom;
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
	outline: none;  # 虚线框
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

# 下面三项需要先设置下面代码
self.QComboBox.currentIndexChanged.connect(self.main_info.usernamechange)
self.QComboBox.setView(QListView())

# 下拉框样式
QListView{outline:0px}

# 下拉框选项
QListView::item{border-bottom:1px solid rgb(230, 230, 230, 1);
				padding-left:2px;
				font-size:12px;
				font-weight:400;
				color:rgba(51,51,51,1);
				line-height:1px;
				height:18px}
				
# 下拉框选项鼠标悬停
QListView::item::hover{background:rgba(245, 245, 254, 0.9)})
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

### 滑块
```
horizontal换成vertical就是垂直的滑块
# 滑块条
QSlider::groove:horizontal{border:none;
                           height:12px}
# 以划过部分						   
QSlider::sub-page:horizontal{background:rgba(0,191,255,0.5);
                             border-radius:6px}
# 未划过部分							 
QSlider::add-page:horizontal{background:rgba(230,230,230,1);
                             border-radius:6px}
# 滑块							 
QSlider::handle:horizontal{background:rgba(0,191,255,1);
                           border:none;
                           width:20px;
                           height:20px;
                           margin-top:-4px;
                           margin-bottom:-4px;
                           border-radius:10px}
```

### 勾选框
```
# 勾选框主体
QCheckBox{font-size:14px;
		  font-family:Microsoft YaHei;
		  font-weight:400;
		  color:rgba(51,51,51,1)}
# 勾选框框体
QCheckBox::indicator{width:18px;
					 height:18px;
					 border:1px solid rgba(179,179,179,1);
					 border-radius:2px}
# 勾选框勾选状态
QCheckBox::indicator:checked{background:rgba(95,184,120,1);
							 image:url()}
```

### 滚动条
```
vertical换成horizontal就是水平的滚动条
预留上下按钮的位子：
padding-top:18px;
padding-bottom:18px

# 滚动条主体
QScrollBar:vertical{background:rgba(238,238,238,1);
				    width:14px;
				    padding-left:1px;
				    padding-right:1px;
				    padding-top:18px;
				    padding-bottom:18px}
# 滑块		
QScrollBar::handle:vertical{background:rgba(204,204,204,1)}
# 已经滑过的区域
QScrollBar::sub-page:vertical{background:rgba(238,238,238,1);}
# 还没滑过的区域
QScrollBar::add-page:vertical{background:rgba(238,238,238,1);}
# 向下按钮
QScrollBar::add-line:vertical{height:18px;
							  width:14px;
							  image:url(image/setup_win/三角_u151.png)}
# 向上按钮							  
QScrollBar::sub-line:vertical{height:18px;
							  width:14px;
							  image:url(image/setup_win/三角_u151 - 副本.png)}
```

### QListWidget列表
```
QListWidget{border:none;
		    outline: none}
# 项			
QListWidget::item{outline: none}
# 鼠标悬停在项上
QListWidget::item:hover{background:rgba(0,0,0,0)}
# 项被选择状态
QListWidget::item:selected{background:rgba(0,0,0,0)
```
>本文用于记录我在工作中常用的控件样式表，后续还会继续更新。