---
title: VPS-用搬瓦工（Bandwagon）搭建VPS实现科学上网
date: 2019-04-12 13:00:58
tags:
- Bandwagon
- VPS
- 网络
categories: 技术向
description: 关于如何上网
cover_img: https://raw.githubusercontent.com/yefcion/PicData/master/img/huaban (1).jpeg
---



## 写在前面

1. FQ是为了学习交流、使用工具
2. 以国为荣，不惹事端
### 起因
网面上所有hosts（以[老D博客](https://laod.cn/hosts/2017-google-hosts.html)为例）不能用，各种VPN被封，导致我所使用的Google系列产品无法使用。最近看到一个朋友的技术贴，觉得FQ这种技术活掌握在手里也挺好，所以我也开始尝试了。
### 介绍
这项活动不难，`按部就班`即可完成。当然，我的做法可能比较小白，无法发掘VPS全部功能，但是就我的初衷`顺利FQ`而言，完美稳定。
简单介绍一下梗概：
1. 购买搬瓦工（Bandwagon）VPS
2. 配置VPS
3. 配置终端，PC/Android/IOS客户端
4. 科学上网

### 1. 购买VPS
    - 知名VPS服务商有：Bandwagon、Vultr 、 Linode。
    - 我使用Bandwagon，原因很简单：便宜，可支付宝。
    - KVM、$25.99 USD包年、USCA_2
    - 可以几个人合买哦，因为配置完之后理论上是没有使用人数限制的，只不过就像宽带一样，人多了速度自然就会慢了

1.1 访问[搬瓦工](https://bwh1.net/index.php)网址
![2017-12-01-13-49-45.jpg](http://upload-images.jianshu.io/upload_images/152050-448ccca37257ccf6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

1.2 选择套餐，加入购物车：
本着价格优先和稳定够用的原则，我选了第二便宜的套餐（最便宜的套餐常年断货）
至于`KVM`还是`OVZ`，推荐KVM，这是一番比较之后的结果。看过的文章忘记保存，以后有机会的话放网址。
之后是套餐内容：
![2017-12-01-13-50-52.jpg](http://upload-images.jianshu.io/upload_images/152050-ceeda80f2fc999a5.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

选择时长和服务器位置：包月、包年；洛杉矶、加拿大、日本等
我选择了`包年`、`洛杉矶_2`

1.3 确认订单与付款
![搬瓦工主页](http://upload-images.jianshu.io/upload_images/152050-7940554988367f0b.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
这里可以填优惠码，用了会打折扣（和国内软件邀请机制一样，双方都有优惠），然后checkout
如果是新用户的话需要注册，要填的信息如下：姓名、邮件、地址、电话。这里可以选择支付宝(Alipay)付款，最后complete order完成。
![2017-12-01-14-04-47.jpg](http://upload-images.jianshu.io/upload_images/152050-069211d54d513c03.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

购买成功后，邮箱收到三封邮件，里面有重要信息（SSH port、VPS IP、root 临时密码）需要记录。之后有用。
以上VPS的购买完成，下面进入配置。

### 2. 配置VPS
2.1 返回home，进入client area，找到service，`my services`,进入`KiwiVM`控制面板
![2017-12-01-13-55-19.jpg](http://upload-images.jianshu.io/upload_images/152050-20cd309768f31124.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![2017-12-01-14-09-36.jpg](http://upload-images.jianshu.io/upload_images/152050-2089379a849ea0da.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

![2017-12-01-14-11-46.jpg](http://upload-images.jianshu.io/upload_images/152050-d6f03a2f0ad69183.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2.2 配置KiwiVM(核心在这里)
    面板中要用到的面板选项下图标出：
![2017-12-01-14-13-33.jpg](http://upload-images.jianshu.io/upload_images/152050-9613cb6d0a43fc5d.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

	- main controls(主控中心)
	- install OS(安装系统)
	- root password modification(根密码修改)
	- KiwiVM password modification(KiwiVM密码修改)
	- Shadowsocks serve(Shadowsocks服务)
	- ShadowsocksR serve(ShadowsocksR服务)

首先，可以修改用户名，这个就是个人喜好的问题，可以不改，之后配置没什么用。（ ~~但如果你用 putty 的话，这个就是登陆的名字，由于这个教程面向我这种小白用户，所以这段话可以忽略~~）
![2017-12-01-14-29-50.jpg](http://upload-images.jianshu.io/upload_images/152050-3119906b81068f92.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

重装服务器系统。这里本来是默认装了CentOS的系统，但是清洁起见还是重装一下。
Main controls面板，单击Stop，然后Install new OS 这里，选择 CentOS-6-x86_64-bbr，勾选 I agree， 点击 Reload。然后看密码，或者修改。

	• Root password modification 这个是装系统之后，搬瓦工随机分配的密码，自己不能修改，`记录这里的信息`（邮箱里也会收到） 
	• KiwiVM password modification 是登陆账号的密码，直接写新密码。这样的话最开始邮件收到的密码就可以不用了。从这步开始购买VPS时的邮件内容就不用了。

常理的话控制面板应该如下，有ShadowsocksR Server和Shadowsocks serve，但是新装的服务器好像没了，因此要手动安装
![mark](http://pb8ci5khn.bkt.clouddn.com/blog/20181201/wMC1ERhfiqAm.png)

安装 ShadowsocksR Server和Shadowsocks serve
直接点击[链接](https://kiwivm.64clouds.com/main-exec.php?mode=extras_shadowsocks)，快速啊安装shadowsocks服务
安装完成之后记录当前界面显示的消息，用客户端FQ就`靠这些信息`了。（另，保存该网址为书签，不知为什么安装shadowsocks后KiviVM面板上没有添加shadowsocks的菜单，所以为了以后方便查看，把这个网址保存书签把）
IP是首页'Main statistic'中的 'IP address'
![2017-12-01-14-43-52.jpg](http://upload-images.jianshu.io/upload_images/152050-4411d763294d3c54.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
以上VPS配置工作就完成了，只要在终端登录就可以实现科学上网了

### 3. 配置终端
客户端下载，点击关键词即可下载。
- PC
    [SSR-4.7.0](https://cache.cdn.bydisk.com/ShadowsocksR-4.7.0-win.7z)
- IOS
    [Wingy](https://itunes.apple.com/us/app/wingy-http-s-socks5-proxy-utility/id1178584911)
    [shadowrocket](https://itunes.apple.com/us/app/shadowrocket/id932747118)
- Android
    [SSR-3.4.0.5](https://qiniucloud.download.storage.bydisk.com/ssr-3.4.0.5.apk)
- MAC
    [ShadowrocksX-NG-R](https://github.com/qinyuhang/ShadowsocksX-NG-R/releases)
    [electron-ssr](https://github.com/erguotou520/electron-ssr/releases)

配置案例：

1. 以win为例：
把ShadowsocksR Server的信息填到客户端，完成单击确定即可。
![2017-12-01-14-58-39.jpg](http://upload-images.jianshu.io/upload_images/152050-82f31cec772d6186.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

2. 以IOS客户端Wing为例：
把ShadowsocksR Server的信息填到客户端，完成单击确定即可。
![2017-12-01-15-03-56.jpg](http://upload-images.jianshu.io/upload_images/152050-06845dd196adc0e7.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


测试网页：
[Google](www.google.com.hk/?hl=zh-cn)
[Facebook](https://www.facebook.com/)

愉快上网。

![Google](http://m.qpic.cn/psb?/V10z9tn33iz4ON/C6K2XHPp5GsV0.nsYSfLEHvXAnU*9orxloyoKmkv56c!/b/dL8AAAAAAAAA&bo=1QS5AgAAAAARB1o!&rf=viewer_4)