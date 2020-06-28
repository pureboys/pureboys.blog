---
title: "Clash 相关操作"
summary: "Clash 相关操作"
date: 2020-06-26T14:19:29+08:00
categories: ["tech"]
---

### clash 设置终端代理

```shell
alias setproxy="export https_proxy=http://127.0.0.1:7890;export http_proxy=http://127.0.0.1:7890;export all_proxy=socks5://127.0.0.1:7891;echo \"Set proxy successfully\" "
alias unsetproxy="unset http_proxy;unset https_proxy;unset all_proxy;echo \"Unset proxy successfully\" " 
```

