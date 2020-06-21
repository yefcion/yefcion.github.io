---
title: Linux 中定时任务命令 crontab 简易使用
date: 2020-05-12 23:46:35
tags:
- Linux
categories: 技术向
description: Linux 中的定时任务
cover_img: https://cdn.jsdelivr.net/gh/yefcion/PicData@master/img/linux_crontab.jpg
---



参考博客：[Linux crontab命令详解](https://www.cnblogs.com/ftl1012/p/crontab.html)

## 我的使用

```powershell
# 查看现有定时任务并编辑
crontab -e
# 注：此时是 vim 界面，直接 i 编辑即可开始设置定时任务

# 每隔 1 分钟打印一次时间到指定文件
*/1 * * * * echo `date` >> /home/yefcion/Documents/time.txt

# 每小时执行指定脚本
0 * * * * /bin/sh /home/yefcion/release.sh 

# 当天 23 点，第二天 0 点到 7 点，每隔 1 分钟执行一次脚本
* 23,00-07/1 * * * /bin/sh /home/script.sh
```



## 详细讲解

```shell
f1 f2 f3 f4 f5 program
```

 - 其中 f1 是表示分钟，f2 表示小时，f3 表示一个月份中的第几日，f4 表示月份，f5 表示一个星期中的第几天。program 表示要执行的程序。
 - 当 f1 为 * 时表示每分钟都要执行 program，f2 为 * 时表示每小时都要执行程序，其馀类推
 - 当 f1 为 a-b 时表示从第 a 分钟到第 b 分钟这段时间内要执行，f2 为 a-b 时表示从第 a 到第 b 小时都要执行，其馀类推
 - 当 f1 为 */n 时表示每 n 分钟个时间间隔执行一次，f2 为 */n 表示每 n 小时个时间间隔执行一次，其馀类推
 - 当 f1 为 a, b, c,... 时表示第 a, b, c,... 分钟要执行，f2 为 a, b, c,... 时表示第 a, b, c...个小时要执行，其馀类推

```shell
*    *    *    *    *
-    -    -    -    -
|    |    |    |    |
|    |    |    |    +----- 星期中星期几 (0 - 7) (星期天 为0)
|    |    |    +---------- 月份 (1 - 12) 
|    |    +--------------- 一个月中的第几天 (1 - 31)
|    +-------------------- 小时 (0 - 23)
+------------------------- 分钟 (0 - 59)
```


```shell
# 每月每天每小时的第 0 分钟执行一次 /bin/ls
0 * * * * /bin/ls

# 在 12 月内, 每天的早上 6 点到 12 点，每隔 3 个小时 0 分钟执行一次 /usr/bin/backup
0 6-12/3 * 12 * /usr/bin/backup

# 周一到周五每天下午 5:00 寄一封信给 alex@domain.name
0 17 * * 1-5 mail -s "hi" alex@domain.name < /tmp/maildata

# 每月每天的午夜 0 点 20 分, 2 点 20 分, 4 点 20 分....执行 echo "haha"
20 0-23/2 * * * echo "haha"

# 每小时的第一分执行 /home/bruce/backup这个文件 
1 * * * * /home/bruce/backup  
```

注意：当程序在你所指定的时间执行后，系统会发一封邮件给当前的用户，显示该程序执行的内容，若是你不希望收到这样的邮件，请在每一行空一格之后加上 `> /dev/null 2>&1` 即可

```shell
20 03 * * * . /etc/profile;/bin/sh /var/www/runoob/test.sh > /dev/null 2>&1 
```