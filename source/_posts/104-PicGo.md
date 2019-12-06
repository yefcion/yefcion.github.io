---
title: PicGo + GitHub 搭建个人图床工具
date: 2019-03-11 22:56:37
tags:
- 图床
- Blog
- GitHub
- PicGo
categories: 技术向
cover_img: http://m.qpic.cn/psb?/V10z9tn33iz4ON/jAbPWYwB0jblsm4yfhCdgFLhs66goczD2.vr2NCRHzk!/b/dL8AAAAAAAAA&bo=TgJhAQAAAAARBxw!&rf=viewer_4
---



使用 GitHhub 搭建的图床，满足个人用户绝大部分需求。方便程度：★★★★☆  配置难度：★★☆☆☆

<!-- more -->



写在前面

> 我以前用的 `七牛云 + Mpic` 的组合，后来由于七牛云测试域名收回，我的图床就废了。以前的好多图片都埋藏在七牛云的服务器上，又气又难过。思考好一段时间，想自己搭服务，但成本有点高，备案的域名 + 服务器一年几百块。对于我这种不靠写字谋生的人而言没有必要，所以就停摆了一段时间。直到今天用 GitHub 搭起了图床，可以说非常开心了。所以跟大家分享一下。

- 方便程度：★★★★☆
- 配置难度：★★☆☆☆
- 适用环境：win + mac + linux
- 需要工具：GitHub 账号 + PicGo 客户端
- 稳定性：背靠 GitHub 和微软，比自建服务器都稳
- 隐私性：这算是唯一缺点，你的图片别人可以访问

## 1. GitHub 仓库设置

> 流程：新建 public 仓库 -> 创建 token -> 复制 token 备用

### 1.1 新建仓库

点击 git 主页右上角的 `+` 创建 `New repository`；

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/20190311222018.png)

填写仓库信息，例如我就创建了一个 `cloudimg` 的仓库。这里注意，仓库得设置为  `Public` 因为后面通过客户端访问算是外部访问，因此无法访问  `Private` ，这样的话图片传上来之后只能存储不能显示。所以要设置为 `Public`。

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/20190311222514.png)

### 1.2 创建 token 并复制保存

此时仓库已经建立，点击右上角头像，然后进入设置；

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/20190311222925.png)

在页面最下找到 `Developer settings`，点击进入；

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/1552314691232.png)

创建 token；

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/20190311223317.png)
填 description（也是随心填），勾选复选框 repo ，接着到页面底部 `Generate token` 就完成了；

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/1552314907794.png)

然后复制生成一串字符 token，这个 token 只出现一次，所以要保存一下（我一般记在微信收藏）。

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/20190311223901.png)

## 2. PicGo 客户端配置

### 2.1 下载&安装

PicGo （目前 2.0.4）是一个开源的图床工具，非常优秀。可以到 git 上下载，但下载速度太慢，所以我放了一个百度云的链接，速度快很多。

git地址：[PicGo](https://github.com/Molunerfinn/PicGo)

Win版下载链接：[百度云](https://pan.baidu.com/s/17KycPMoqNCnc1cR_yQO8nQ) 密码：PicG

### 2.2 配置

先上图

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/20190311224443.png)

- 用户名 即你的用户名 如我的 `yefcion`
- 仓库名 即你的仓库名 如我的 `PicData`
- 分支名 默认 `master`  
- Token 就是刚刚复制的那一串字符
- 存储路径 这个可以填也可以不填，填了的话图片就上传到 git 中 `data`  这个文件夹
- 域名 `https://raw.githubusercontent.com/yefcion/PicData/master `这个要改一下 格式 `https://raw.githubusercontent.com/[用户名]/[仓库名]/master`

然后点确定就可以了。



然后关于上传的快捷键设置。默认的是 Mac 按键，推荐改成 `Ctrl + alt +c `。

![](https://raw.githubusercontent.com/yefcion/PicData/master/img/20190311224935.png)

综上，操作完成。

本方案唯一缺点，不能私人。但是考虑到 GitHub 上传的图在列表里没法预览，应该没人会闲着没事翻记录。