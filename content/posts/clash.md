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


### clash 自动更新订阅

`clash.service`
```
# edit and save this file to /usr/lib/systemd/system/clash.service
[Unit]
Description=clash
After=network.target

[Service]
WorkingDirectory="your home directory"/.config/clash
ExecStart="your home directory"/.config/clash/start-clash.sh
ExecStop="your home directory"/.config/clash/stop-clash.sh
Environment="HOME=your home directory"
Environment="CLASH_URL=your subscribe address"

[Install]
WantedBy=multi-user.target
```

`start-clash.sh`
```shell
#!/bin/bash
# save this file to ${HOME}/.config/clash/start-clash.sh

# save pid file
echo $$ > ${HOME}/.config/clash/clash.pid

diff ${HOME}/.config/clash/config.yaml <(curl -s ${CLASH_URL})
if [ "$?" == 0 ]
then
    /usr/bin/clash
else
    TIME=`date '+%Y-%m-%d %H:%M:%S'`
    cp ${HOME}/.config/clash/config.yaml "${HOME}/.config/clash/config.yaml.bak${TIME}"
    curl -L -o ${HOME}/.config/clash/config.yaml ${CLASH_URL}
    /usr/bin/clash
fi
```

`stop-clash.sh`
```shell
#!/bin/bash
# save this file to ${HOME}/.config/clash/stop-clash.sh

# read pid file
PID=`cat ${HOME}/.config/clash/clash.pid`
kill -9 ${PID}
rm ${HOME}/.config/clash/clash.pid
```

配置添加完成后，执行以下代码就可以启动Clash并设置为开机自启动。
```shell
systemctl enable clash
systemctl start clash
```



