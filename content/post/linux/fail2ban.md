---
title: "fail2ban 保护ss "
summary: "防止扫描、破解"
date: 2020-12-24T11:12:53+08:00
categories: ["linux"]
---

### 前略

1. 本文使用的是 `shadowsocks-go`
2. fail2ban 安装省略
3. ss日志目录在 `/root/script/ss-log`

### ss 错误格式 
`2020/12/23 23:02:02 error getting request 101.227.27.183:58512 xx.xx.xx.xx:xxxx addr type 0 not supported`

### fail2ban 端设置

1. 新建一个规则

```
sudo vi /etc/fail2ban/filter.d/shadowsocks.conf
```

添加内容:
```
[INCLUDES]
before = common.conf

[Definition]
_daemon = shadowsocks
failregex = ^\s+error getting request <HOST>:\d+ \s*

ignoreregex =
```


2. failregex 验证
```
fail2ban-regex '2020/12/23 23:02:02 error getting request 101.227.27.183:58512 10.0.0.11:9999 addr type 0 not supported' '^\s+error getting request <HOST>:\d+ \s*' 
```

3. 更新 fail2ban 配置

`sudo vi /etc/fail2ban/jail.loca`l

```
[shadowsocks]
enabled = true
filter = shadowsocks
port = 0:65535
logpath = /root/script/ss-log
maxretry = 5
bantime = 43200
```

### 重启 fail2ban

`systemctl restart fail2ban.service`

### 查看监控状态

`sudo fail2ban-client status shadowsocks`
















