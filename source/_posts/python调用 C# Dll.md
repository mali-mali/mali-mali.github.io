---
title: python调用 C# Dll
date: 2020-03-22 10:30
updated: 2020-03-22 10:30
tags:
- python
- C# Dll
---

## 代码
[参考](https://blog.csdn.net/LTG01/article/details/80700513)
```
import clr  #clr是公共运行时环境，这个模块是与C#交互的核心
clr.FindAssembly("PythonNetTest.dll") ## 加载c#dll文件
 
from PythonNetTest import *    # 导入命名空间
instance = Class1() #class1是dll里面的类
print(instance.AddShort(2, 3))#一个简单的加法
```
