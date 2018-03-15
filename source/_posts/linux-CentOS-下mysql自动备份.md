---
title: linux(CentOS) 下mysql自动备份
date: 2018-03-15 18:08:36
tags: [linux,CentOS,Mysql,自动备份]
categories: Mysql
---

1. 创建并编辑文件/data/mysqlBack/bakmysql
```
db_user="root"
db_passwd="root"
db_name="db_test"
# the directory for story your backup file.you shall change this dir
backup_dir="/data/mysqlBack"
# date format for backup file (dd-mm-yyyy)
time="$(date +"%Y%m%d%H%M%S")"     

mysqldump -u$db_user  -p$db_passwd $db_name  > "$backup_dir/$db_name"_"$time.sql"
```
*注意*：-p和$db_passwd之间没有空格，不然$db_passwd会被当做参数[数据库名]来解析

<!--more-->

2. 修改文件backmysql属性，使其可执行
```
chmod +x /usr/sbin/bakmysql
```

3. 创建定时任务
```
crontab -e

 #进入编辑界面，内容如下

 00 22 * * * /data/mysqlBack/backmysq
 #每天22:00执行backmysq文件，备份数据库在路径/data/mysqlBack/下
```
