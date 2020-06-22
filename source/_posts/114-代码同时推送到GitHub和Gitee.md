---
title: 代码同时推送到 GitHub 和 Gitee
date: 2020-06-16 23:08:35
tags:
- git
- GitHub
- Gitee
categories: 技术向
description: 两个仓库的代码推送
cover_img: https://cdn.jsdelivr.net/gh/yefcion/PicData@master/img/push_git.jpg
---




> 写在前面
>
> 本文运行前提：已有 GitHub 仓库，并在 Gitee 上克隆了该仓库。GitHub 和 Gitee 用的同一个邮箱。



## 配置全局环境

首先配置本地邮箱用户名等

然后生成 ssh-key 并在两个网站加入这个 key

```bash
ssh-keygen -t rsa -C "your_email@example.com"
```

在 key 生成的路径(我的是 `C:\Users\yefci\.ssh`\)下新建 config 文件，里面添加内容如下

```bash
# gitee
Host gitee.com
HostName gitee.com
IdentityFile C:\Users\yefci\.ssh\id_rsa

# github
Host github.com
HostName github.com
IdentityFile C:\Users\yefci\.ssh\id_rsa
```



## 配置项目

然后到想要的目录下拉一份项目代码，这里的项目是 `git@gitee.com:yefcion/Poweron.git`

```bash
# 1 查看仓库信息
git remote -v

# 2 删除原有远程仓库，然后添加两个远程仓库
git remote rm origin
git remote add github git@github.com:yefcion/Poweron.git
git remote add github git@gitee.com:yefcion/Poweron.git

# 2 拉代码，合并两个仓库的历史记录
git pull gitee master --allow-unrelated-histories
git pull github master --allow-unrelated-histories

# 3 push 代码
git push gitee master
git push github master
```



对于提交操作，为了避免以后每次提交都 push 两次，可以写个脚本一起提交

```bash
# 脚本内容
vim push.sh
	echo Gitee
	git push gitee master
	echo
	echo GitHub
	git push github master

# 提交代码
sh push.sh
```





