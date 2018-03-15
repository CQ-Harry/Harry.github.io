---
title: win10 安装 mysql-5.7.19-winx64
date: 2018-03-15 17:29:22
tags: [win10,mysql]
categories: [win10,mysql]
---

- 下载地址：

[https://dev.mysql.com/downloads/mysql/](https://dev.mysql.com/downloads/mysql/)

- 下载：

![Alt text](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512113967774_1.png)

<!--more-->

- 解压到指定的路径

![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512114126430_1.png)

上图红框中的**my.ini**以及**data**文件夹需要自己添加
	**my.ini**内容如下：
```
[client]
port=3306
default-character-set=utf8

[mysqld]
# 设置为自己MYSQL的安装目录 
basedir=E:\Program Files (x86)\MySQL\mysql-5.7.19-winx64
# 设置为MYSQL的数据目录
datadir=E:\Program Files (x86)\MySQL\mysql-5.7.19-winx64\data
port=3306
character_set_server=utf8
sql_mode=NO_ENGINE_SUBSTITUTION,NO_AUTO_CREATE_USER
#开启查询缓存
explicit_defaults_for_timestamp=true
skip-grant-tables
```
- 设置环境变量
	![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512114228199_1.png)

- 开始安装:
	用**管理员**身份打开cmd，并进入mysql安装目录下的bin
```
#初始化data目录下的相关文件
mysqld –initialize-insecure

#安装
mysqld install

#启动
net start mysql

#修改密码
set password for root@localhost = password(‘newPassword’);

#登录
mysql -u root -p
```

- 完工
	![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512114302800_1.png)
	![id](http://cqhui.oss-cn-shenzhen.aliyuncs.com/1512114335965_1.png)
