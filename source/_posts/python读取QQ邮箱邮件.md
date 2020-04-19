---
title: python读取QQ邮箱邮件
date: 2020-04-19 10:00
updated: 2020-04-19 10:00
tags:
- python
- 邮箱
---

工作上遇到一个需要进行邮箱操作的需求，这里我以QQ邮箱为例记录下我的代码案例：

## 代码案例

### 首先引用我们需要的库

```
import poplib
from email.parser import Parser
from email.header import decode_header
from email.utils import parseaddr

```

### 然后是登录
```
def login():
	server = poplib.POP3_SSL(host_server)
	# 可以打开或关闭调试信息:
	# server.set_debuglevel(0)
	# 可选:打印POP3服务器的欢迎文字:
	# print(server.getwelcome().decode('utf-8'))
	# 身份认证:
	server.user('username')
	server.pass_('password')
	return server
```
>需要注意，应为我使用的是QQ邮箱所以这里的密码不是邮箱的登录密码，而是授权码。授权码需要在QQ邮箱中设置。[设置方式]（https://service.mail.qq.com/cgi-bin/help?subtype=1&&no=308&&id=26）

### 获取邮箱中的右键列表
```
def getemaillist(server):

	# server.stat()  # 返回邮件数量和占用空间:
	
	resp, mails, octets = server.list()  # 返回所有邮件的编号:

	# print(mails)  # 可以查看返回的列表类似[b'1 82923', b'2 2184', ...]
	
	return mails
```

### 获取邮件内容
```
def getEmail(server, index):
	# index : 要获取的邮件
    try:
        resp, lines, octets = server.retr(index)
        # lines存储了邮件的原始文本的每一行,
        # 可以获得整个邮件的原始文本:
        msg_content = b'\r\n'.join(lines).decode('utf-8')
        # 稍后解析出邮件:
        msg = Parser().parsestr(msg_content)
        return print_info(msg)
    except Exception as e:
        return {}


def print_info(msg, indent=0):
    email_data = {}
    if indent == 0:
        for header in ['From', 'To', 'Date', 'Subject']:
            value = msg.get(header, '')
            if value:
                if header == 'Subject':
                    value = decode_str(value)
                elif header == 'From' or header == 'To':
                    hdr, addr = parseaddr(value)
                    name = decode_str(hdr)
                    value = u'%s <%s>' % (name, addr)
                elif header == 'Date':
                    week, dt = GreenwichTime(value)
                    value = dt.strftime("%Y-%m-%d %H:%M:%S")
                    email_data['Week'] = week
            email_data[header] = value

    if msg.is_multipart():
        parts = msg.get_payload()
        for n, part in enumerate(parts):
            print_info(part, indent + 1)
    else:
        content_type = msg.get_content_type()
        if content_type == 'text/plain' or content_type == 'text/html':
            content = msg.get_payload(decode=True)
            charset = guess_charset(msg)
            if charset:
                content = content.decode(charset)
            email_data[content_type] = content
        else:
            email_data[content_type] = content_type
    return email_data
```

