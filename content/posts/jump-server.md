---
title: "一步登陆 jumpserver 跳板机 告别Google的身份验证器"
summary: "一步登陆 jumpserver 跳板机 告别Google的身份验证器"
date: 2020-06-22T09:37:33+08:00
categories: ["tech"]
---

### 起因

jumpserver 登陆时间有限制， 过段时间就会被登出， 每次登陆都要输入Google的身份验证器的验证码。非常的不方便。


### 解决方案

网上找到了方案 使用 expect 和 oathtool 去解决问题：

1. expect 是自动化控制和测试的工具，可以使用脚本来对终端进行自动化控制。 https://zh.wikipedia.org/wiki/Expect
2. oathtool 应该是Google的身份验证器加密的一种方式 （猜的） https://www.nongnu.org/oath-toolkit/man-oathtool.html

### 使用python实现代码

1. 环境安装
```shell
pip install oathtool
pip install pexpect
```

2. 代码部分
```python
import pexpect
import oathtool
import struct
import fcntl
import sys
import termios

# 此函数用来设置合适的窗口大小
def getwinsize():
    """This returns the window size of the child tty.
    The return value is a tuple of (rows, cols).
    """
    if 'TIOCGWINSZ' in dir(termios):
        TIOCGWINSZ = termios.TIOCGWINSZ
    else:
        TIOCGWINSZ = 1074295912  # Assume
    s = struct.pack('HHHH', 0, 0, 0, 0)
    x = fcntl.ioctl(sys.stdout.fileno(), TIOCGWINSZ, s)
    return struct.unpack('HHHH', x)[0:2]


child = pexpect.spawn('ssh your-user@your-host ')
child.expect('MFA')  # 这个判断你想要出现字符， 我这里是MFA
passwd = oathtool.generate_otp("你的身份验证器的密钥")
child.sendline(passwd)  # 发送验证码

# 自适应窗口大小， 防止vim等显示的数据错乱，串行等
winsize = getwinsize() 
child.setwinsize(winsize[0], winsize[1])

child.interact()  # 把ssh的连接交给用户控制。
child.close()
```

3. 写好 alias 这里省略
