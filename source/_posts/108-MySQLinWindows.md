---
title: Win10 下安装 MySQL 8.0.17
date: 2019-10-09 22:56:37
tags:
- MySQL
categories: 技术向
cover_img: http://m.qpic.cn/psb?/V10z9tn33iz4ON/kVPQbLrsXY5eImjnxzMdgWtDRMVCOdEz*fSmZkbbChc!/b/dLYAAAAAAAAA&bo=7gIsAQAAAAARB*E!&rf=viewer_4


---



写在前面

> 因为之前装过很多次，均以失败告终，这次一次成功很意外，所以记录一下。



## 1. 下载&安装

下载地址：https://dev.mysql.com/downloads/mysql/

MySQL 不需要安装，下载 zip 包解压放到指定路径即可。

如，我的解压路径是：`D:\ProjectV\mysql-8.0.17-winx64`

## 2. 配置环境变量

**首先**新增变量：

变量名：MYSQL_HOME

变量值：D:\ProjectV\mysql-8.0.17-winx64

**然后**到path里

添加：%MYSQL_HOME%\bin;

## 3. 生成 data 文件

`以管理员身份`运行 cmd，一定要用管理员身份。进入 MySQL 安装目录的下 bin 目录，执行以下命令生成 data 目录。

```cmd
# 进入 bin 目录
cd /d D:\ProjectV\mysql-8.0.17-winx64\bin
# 生成 data 目录
mysqld --initialize-insecure --user=mysql 
```

## 4. 启动服务

执行命令：`net start mysql` 启动mysql服务，若提示：服务名无效...(后面有解决方法==步骤：5）

## 5. 解决启动服务失败（报错）

提示：服务名无效

解决方法：

执行命令：`mysqld -install`  即可

（不需要my.ini配置文件 注意：网上写的很多需要my.ini配置文件，其实不需要my.ini配置文件也可以，我之前放置了my.ini文件，反而提示服务无法启动，把my.ini删除后启动成功了）

若出现下图，需要去资源管理器中把mysql进程全结束了，重新启动即可。

 ![img](https://images2018.cnblogs.com/blog/1283788/201803/1283788-20180312210208531-2077935748.png)

## 6. 登录mysql

```cmd
mysql -u root -p

# Enter password:
# 这里由于刚安装，所以密码为空，不用输入密码，直接回车即可
```

![登录](http://m.qpic.cn/psb?/V10z9tn33iz4ON/v6YRmEl8iLImvqOcJB0Mmyou5Lrv6qO8rrXZpg.hQy4!/b/dMMAAAAAAAAA&bo=owL0AAAAAAARB2U!&rf=viewer_4)



## 7. 查询用户密码

查询用户密码命令：

```mysql
select host,user,authentication_string from mysql.user;
```



 ![img](https://images2018.cnblogs.com/blog/1283788/201803/1283788-20180312210245857-1758917794.png)

## 8. 设置（或修改）root用户密码

设置（或修改）root用户密码：

```mysql
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '123456';
# BY 后引号中的内容是密码，自己可以随便设置

flush privileges; 
# 相当于保存，执行此命令后，设置才生效，若不执行，还是之前的密码不变
```

Query OK, 0 rows affected (0.01 sec) 

## 9. 退出mysql

```mysql
quit
```

