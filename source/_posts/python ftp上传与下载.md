---
title: python ftp上传与下载
date: 2020-04-26 10:00
updated: 2020-04-26 10:00
tags:
- python
- ftp
---

## 上传

```

```

## 下载
这里我们先封装一个继承至ftp的类，用于显示文件下载的进度。 这里我用到了QThread是应为我的项目使用了qt开发，而特意将下载过程放到子线程进行再使用qt的信号机制更新UI。
```
from ftplib import FTP
from PyQt5.QtCore import QThread, pyqtSignal


host = '127.0.0.1'
port = 21
username = 'xxx'
password = '123456'


class ftpClient(FTP):
    """
    cmd:命令
    callback:回调函数
    fsize:服务器中文件总大小
    rest:已传送文件大小
    """
    signal = None

    def retrbinary(self, cmd, callback, fsize=0, rest=0):
        cmpsize = rest
        self.voidcmd('TYPE I')
        # 此命令实现从指定位置开始下载,以达到续传的目的
        conn = self.transfercmd(cmd, rest)
        while 1:
            try:
                if fsize:
                    if (fsize - cmpsize) >= 1024:
                        blocksize = 1024
                    else:
                        blocksize = fsize - cmpsize
                    ret = float(cmpsize) / fsize
                    num = ret * 100
                    # 实现同一行打印下载进度
                    # print('下载进度: %.2f%%' % num)
                    data = conn.recv(blocksize)
                    if self.signal:
                        if num < 100:
                            self.signal.emit(int(num), 'loading')
                        else:
                            self.signal.emit(int(num), 'true')
                    if not data:
                        break
                    callback(data)
                    cmpsize += len(data)
            except Exception as e:
                if self.signal:
                    self.signal.emit(0, 'false')
        conn.close()
        return self.voidresp()


class download(QThread):
    """
    remote_path:目标地址
    local_path:本地保存地址
    """
	
    download_proess_signal = pyqtSignal(int, str)

    def __init__(self, remote_path, local_path):
        super(download, self).__init__()
        self.remote_path = remote_path
        self.local_path = local_path
        self.ftp = ftpClient()
        self.ftp.signal = self.download_proess_signal
        self.ftp.connect(host, port)  # 第一个参数可以是ftp服务器的ip或者域名，第二个参数为ftp服务器的连接端口，默认为21
        self.ftp.login(username, password)  # 匿名登录直接使用ftp.login()

    def run(self):
        """下载ftp资源"""
        fp = open(self.local_path, "wb")
        buf_size = self.ftp.size(self.remote_path)
        self.ftp.retrbinary('RETR {}'.format(self.remote_path), fp.write, buf_size)
        fp.close()
```