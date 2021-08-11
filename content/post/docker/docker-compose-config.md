---
title: "docker 配置文件"
summary: "docker 配置文件"
date: 2021-08-11T10:18:26+08:00
categories: ["docker"]
---

### 配置文件路径


```shell
sudo vim /etc/docker/daemon.json
```

###  修改配置文件


```shell
{
    "registry-mirrors": [
        "https://1nj0zren.mirror.aliyuncs.com",
        "https://docker.mirrors.ustc.edu.cn",
        "http://f1361db2.m.daocloud.io",
        "https://dockerhub.azk8s.cn"
    ],
    "data-root": "/datafile/docker",
    "default-address-pools": [{"base":"10.10.0.0/16", "size": 24}]
}
```

* `registry-mirrors` 设置镜像加速地址
* `data-root` 设置docker文件存储位置
* `default-address-pools` 设置docker默认网段地址


