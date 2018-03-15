---
title: mysql数据库、Java服务端对emoji的支持
date: 2018-03-15 18:05:58
tags: [Mysql,Java,emoji]
categories: Mysql
---

### 问题描述：
如果UTF8字符集且是Java服务器的话，当存储含有emoji表情时，会抛出类似如下异常：
```
java.sql.SQLException: Incorrect string value: '\xF0\x9F\x92\x94' for column 'name' at row 1  
    at com.mysql.jdbc.SQLError.createSQLException(SQLError.java:1073)  
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3593)  
    at com.mysql.jdbc.MysqlIO.checkErrorPacket(MysqlIO.java:3525)  
    at com.mysql.jdbc.MysqlIO.sendCommand(MysqlIO.java:1986)  
    at com.mysql.jdbc.MysqlIO.sqlQueryDirect(MysqlIO.java:2140)  
    at com.mysql.jdbc.ConnectionImpl.execSQL(ConnectionImpl.java:2620)  
    at com.mysql.jdbc.StatementImpl.executeUpdate(StatementImpl.java:1662)  
    at com.mysql.jdbc.StatementImpl.executeUpdate(StatementImpl.java:1581)
```

<!--more-->

这就是字符集不支持的异常。因为UTF-8编码有可能是两个、三个、四个字节，其中Emoji表情是4个字节，而Mysql的utf8编码最多3个字节，所以导致了数据插不进去。

### 实施步骤：
1. utf8mb4的最低mysql版本支持版本为5.5.3+，若不是，请升级到较新版本。
2. 修改database、table和column字符集。参考以下语句：
```
ALTER DATABASE database_name CHARACTER SET = utf8mb4 COLLATE = utf8mb4_unicode_ci;
ALTER TABLE table_name CONVERT TO CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
ALTER TABLE user CHANGE nick_name nick_name varchar(50) CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```
3. 修改mysql配置文件my.cnf（windows为my.ini）
###### my.cnf一般在etc/mysql/my.cnf位置。找到后请在以下三部分里添加如下内容：
```
[client]
default-character-set = utf8mb4
[mysql]
default-character-set = utf8mb4
[mysqld]
character-set-client-handshake = FALSE
character-set-server = utf8mb4
collation-server = utf8mb4_unicode_ci
init_connect='SET NAMES utf8mb4'
```
4. 重启 MySQL Server、检查字符集
5. 如果你用的是java服务器，升级或确保你的mysql connector版本高于5.1.13，否则仍然无法使用utf8mb4
6. 检查你服务端的db配置文件：
```
jdbc.driverClassName=com.mysql.jdbc.Driver
jdbc.url=jdbc:mysql://localhost:3306/database?autoReconnect=true&useUnicode=true&zeroDateTimeBehavior=convertToNull
jdbc.username=root
jdbc.password=password
```
**特别说明其中的jdbc.url配置：如果你已经升级好了mysql-connector，其中的characterEncoding=utf8可以被自动被识别为utf8mb4（当然也兼容原来的utf8），而autoReconnect配置强烈建议配上，之前就是忽略了这个属性，导致因为缓存缘故，没有读取到DB最新配置，导致一直无法使用utf8mb4字符集！！这里还有一个坑，费了我好长时间。connector版本大于5.1.13的不能加characterEncoding=utf8。**

来自于：[mysql/Java服务端对emoji的支持](https://segmentfault.com/a/1190000000616820)