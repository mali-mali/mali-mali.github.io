---
title: 使用QtWebEngineWidgets下载网页资源
date: 2020-04-05 10:00
updated: 2020-04-05 10:00
tags:
- PyQt5
- QtWebEngineWidgets
- Web浏览器
---

## 代码案例
```
	def __init__(self):
        super(my_Browser, self).__init__(None)
        self.page().profile().downloadRequested.connect(self.on_downloadRequested)  # 页面下载请求

    def on_downloadRequested(self, downloadItem):
        if downloadItem.isFinished() == False and downloadItem.state() == 0:
            ###生成文件存储地址
            the_filename = downloadItem.url().fileName()

            the_sourceFile = os.path.join('D:\\', the_filename)

            ###下载文件
            downloadItem.setPath(the_sourceFile)
            downloadItem.accept()
            downloadItem.finished.connect(self.on_downloadfinished)

        #  下载结束触发函数

    def on_downloadfinished(self):
        js_string = '''
               alert("下载成功，请到软件同目录下，查找下载文件！");
               '''
        self.page().runJavaScript(js_string)
```
