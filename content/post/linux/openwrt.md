---
title: "Openwrt 命令行配置上网并安装web界面"
summary: "Openwrt命令行配置上网并安装web界面"
date: 2020-09-06T19:12:53+08:00
categories: ["linux"]
---

###  Openwrt 命令行配置上网并安装web界面

```shell
# 登录路由器
ssh root@192.168.1.1

#更改密码
passwd

#配置文件地址
/etc/config/network

# pppoe设置上网帐号
uci set network.wan.proto=pppoe
uci set network.wan.username=帐号
uci set network.wan.password=密码

# 重启网络
/etc/init.d/network restart

# 是否能正常上网
ping 8.8.8.8

# 更新安装源
opkg update

# 安装web插件
opkg install --force-depends luci
/etc/init.d/uhttpd enable
/etc/init.d/uhttpd start

# 登录网页
http://192.168.1.1/cgi-bin/luci/



```

