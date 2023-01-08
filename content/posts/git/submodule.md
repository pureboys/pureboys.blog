---
title: "Git 子模块 Submodule 教程"
summary: "Git 子模块 Submodule 教程"
date: 2020-06-26T14:52:28+08:00
categories: ["git"]
---


###  git命令

### 添加子模块

``` shell
git submodule add git@github.com:MeiK2333/github-style.git themes/github-style
```
然后可以看到目录下出现了.gitmodules文件，其中记录了子模块保持地址及Url

``` shell
[submodule "themes/github-style"]
	path = themes/github-style
	url = https://github.com/MeiK2333/github-style.git
```

### 查看子模块
``` shell
git submodule
```

### 初始化及更新子模块
``` shell
git submodule init
//初始化本地的子模块配置文件，在拉取了一个带有子模块的工程后，需要先执行init

git submodule update
//更新子模块（如子模块文件夹为空，则会下载子模块）
```

### 提交子模块修改
当子模块有修改时，在主项目上git status并不会完全显示修改的文件，只会显示被修改的子文件夹，如
``` shell
modified:   themes/github-style (modified content)
```
然后需要进入子模块进行常规的add，commit等操作，将子模块的改动提交
``` shell
cd themes/github-style
git add --a
git commit -s -m"Modify submodule"
```

此时回到主工程, 用diff语句查看改动，可以看到改动的是子模块最新的commit ID，这可以理解为主工程保留了每个子模块最新的commit ID指针，当运行git submodule update时，就是将每个子模块更新至各自最新的commit ID

``` shell
git diff themes/github-style
```
将这个改动提交，那么远程仓库也就更新了子模块的最新指针。

### 删除子模块
1. 删除.gitmodules中的子模块内容
2. 删除.git/config中的子模块内容
3. 删除.git/modules中的子模块对应文件夹
4. 运行git rm --cached -r themes/next删除对应子模块文件夹的索引
5. 删除子模块文件夹

### 常见问题

1. xxx already exists in the index
出现在git submodule add时，说明同一文件夹路径下有其他子模块。如果已经手动删除，错误仍然存在，可能是路径索引导致，需要完整执行删除子模块操作。

2. server does not allow request for unadvertised object
出现在git submodule update时，说明主工程中保持的最新的子模块commit id，无法在服务器子模块工程中找到，可能是因为子模块改动未提交，或者提交分支错误导致commit id改变。


### Refer
https://xuejiaw.github.io/gitSubmoduleTutorial/







