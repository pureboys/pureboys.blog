---
title: "如何为Linux新增swap交换空间"
summary: "如何为Linux新增swap交换空间"
date: 2020-10-10T21:28:21+08:00
categories: ["tech"]
---

## 概述

在Linux上增加swap空间的方法有两种：

1. 在安装时手动分配swap，对于这种方式，空间大小是事先规划好的，每次系统启动时会自动进行挂载，但这种方法只能在安装OS时设定，一旦设定好不容易改变，除非重装系统。
2. 使用`swapfile`: 新建临时`swapfile`或者是空闲分区，在需要的时候设定为交换空间，最多可以增加8个`swapfile`。

`Redhat6.4官方`推荐的系统交换空间:

| 系统中的RAM大小 | 建议交换空间  | 如果允许休眠建议使用交换空间 |
| --------------- | ------------- | ---------------------------- |
| ⩽`2GB`          | RAM的2倍      | RAM数量的3倍                 |
| > `2GB - 8GB`   | 等于RAM的数量 | RAM的2倍                     |
| > `8GB - 64GB`  | 至少4 GB      | RAM的1.5倍                   |
| > `64GB`        | 至少4 GB      | 不建议休眠                   |

因为有些系统会允许休眠状态，如果不设置swap会无法启动，就像windows系统使用休眠设置虚拟内存一样。使用这种方法比较灵活，也比较方便，缺点是启动系统后需要手工设置。

还有其他一些博客中有下面一个推荐设置，我也不清楚是怎么得到这个标准的，是否合理也无从考证，可以作为一个参考：

| 系统中的RAM大小 | 建议交换空间         |
| --------------- | -------------------- |
| ⩽ `4G`          | SWAP 设置为内存的2倍 |
| `4G-8G`         | SWAP 等于内存大小    |
| `8G-64G`        | SWAP 设置为`8G`      |
| `64G-256G`      | SWAP 设置为`16G`     |

如何选择可以考虑以下几个情况：

- 物理内存空间是否在峰值也可以满足要求，不满足建议设置swap防止内存溢出导致主机宕机，当然设置不宜过大，占用磁盘空间。
- 磁盘I/O是否足够，使用磁盘I/O值过低或者其他业务要求高，不建议设置swap。由于使用swap需要频繁写入磁盘，使用固态硬盘不建议设置（固态硬盘PE写入次数有限，因此寿命要比机械硬盘更低，考虑到使用寿命当然就尽量减少写入次数）
- 服务器安装的具体的应用，比如`Mysql`等不推荐设置，可以不设置或者修改参数`swappiness`来控制的，默认值为 60意思是说在内存使用40%的情况下就使用swap空间，可以将值设置为10 或者设置0，优先使用物理内存。

对于我使用`manjaor linux`作为日常操作系统用的就没有上述的考虑了，根据自己的使用情况，`8G`内存我设置`8G`或者`2G`其实都够用

分配`swapfile`作为交换空间步骤如下：

## 步骤

1. 首先运行

```shell
free -h
```

查看一下物理内存大小和swap

```
             总计         已用        空闲       共享    	   缓冲/缓存    可用
内存：       7.7Gi       2.3Gi       2.3Gi       612Mi       3.1Gi       4.5Gi
交换：       0Gi          	0B       	0Gi
```

我的显示`8G`并没有分配`swap`空间所以为0

1. 创建`swapfile`文件

```shell
# 切换到根路径
$ cd /
# 这里只需要改count的大小即可比如分配8G就是8，bs是单位
# swapfile的位置是/var/swapfile也可以自己修改，名称也可以不叫swapfile,自己指定
$ sudo dd if=/dev/zero of=/var/swapfile bs=1G count=8
8+0 的读入
8+0 的写出
8589934592 bytes (8.1 GB) copied, 40.55445 s, 202 MB/s
```

`mkswap`创建交换文件

```shell
$ sudo mkswap /var/swapfile
```

`swapon`激活

```shell
 $ swapon /var/swapfile
```

查看增加后swap空间

```shell
$ free -h
			总计         已用        空闲      共享    		缓冲/缓存    可用
内存：       7.7Gi       2.3Gi       2.3Gi    612Mi       3.1Gi       4.5Gi
交换：       8Gi          	0B       	8Gi
```

设置开机自动挂载

```shell
$ sudo vim /etc/fstab
```

在其中添加一行

```shell
/var/swapfile			swap			swap	defaults 0 0
```

添加之后我的完整fstab文件如下，自行参考：

```shell
# /etc/fstab: static file system information.
#
# Use 'blkid' to print the universally unique identifier for a device; this may
# be used with UUID= as a more robust way to name devices that works even if
# disks are added and removed. See fstab(5).
#
# <file system>             <mount point>  <type>  <options>  <dump>  <pass>
/var/swapfile			swap			swap	defaults 0 0
/dev/sdb3			/home/guqing/guqing	ext4	defaults 0 0
UUID=A402-DC5E                            /boot/efi      vfat    umask=0077 0 2
UUID=21d03dc0-948a-4adc-969c-3160a29aeef2 /              ext4    defaults,noatime 0 1
```

其中`/dev/sdb3`一行是因为我挂载了磁盘的缘故

然后执行,自动挂载所有在`fstab`中的文件系统

```shell
 $ mount -a
```

到此就可以使用`swapfile`了

## 去掉swap

如果不想使用swap空间了，可以使用如下方法删除`swapfile`

```shell
# 停用
$ sudo swapoff /var/swapfile

# 删除swapfile,路径为创建时制定的，/var/swapfile
$ sudo rm -f /var/swapfile

# 确认
$ free -h
```

## Refer
https://guqing.xyz/archives/linux-swapfile