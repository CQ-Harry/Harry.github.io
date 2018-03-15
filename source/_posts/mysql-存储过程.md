---
title: mysql 存储实例
date: 2018-03-15 22:46:04
tags: [Mysql,存储过程]
categories: Mysql
---


### sql拼接注意：
#### 单双引号
#### like查询
<!--more-->
#### 临时表更新涉及到时间的字段最好加默认时间：
```
	//数据更新的时候，时间会同步更新
	create_time timestamp not null,
	//更改后
	create_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
```
#### 能通过拼接实现的sql，尽量不用‘？’来替换参数
```
//参数替换
set @ssra = CONCAT(@ssra,'where u.parent_id = ?');
EXECUTE sqlquery USING @parent_id;
//SQL拼接
set @ssra = CONCAT(@ssra," and u.nick_name like '%",@user_name,"%'");
```
***参数值是desc、asc，实现前端传值排序，目前只知道拼接来实现，‘？’形式会抱错***

### 完整例子
```
-- 导出 yiyi_db 的数据库结构
CREATE DATABASE IF NOT EXISTS `yiyi_db` /*!40100 DEFAULT CHARACTER SET utf8 */;
USE `yiyi_db`;

-- 导出  过程 yiyi_db.get_my_promotion_list 结构
DELIMITER //
CREATE DEFINER=`root`@`%` PROCEDURE `get_my_promotion_list`(
	IN `user_name` VARCHAR(50),
	IN `parent_id` CHAR(32),
	IN `time_order_by` VARCHAR(20),
	IN `deal_order_by` VARCHAR(20),
	IN `page_index` INT,
	IN `size` INT
)
BEGIN
	set @user_name = user_name;
	set @parent_id = parent_id;
	set @time_order_by = time_order_by;
	set @deal_order_by = deal_order_by;
	set @page_index = page_index;
	set @size = size;
		
	create temporary table if not exists user_tmp(
		id char(32) not null,
		nick_name VARCHAR(100) DEFAULT NULL,
		upyun_url varchar(300) DEFAULT NULL,
		create_time TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
		parent_id char(32) not null,
		volume bigint not null default 0
	)ENGINE = MEMORY ;

	begin 
		set @ssra = CONCAT('insert into user_tmp(id,nick_name,upyun_url,create_time,parent_id ) ');
		set @ssra = CONCAT(@ssra,'SELECT ');
		set @ssra = CONCAT(@ssra,'u.id,u.nick_name,u.upyun_url,u.create_time,u.parent_id ');
		set @ssra = CONCAT(@ssra,'from user as u ');
		set @ssra = CONCAT(@ssra,'where u.parent_id = ?');
		if @user_name <> '' then
		set @ssra = CONCAT(@ssra," and u.nick_name like '%",@user_name,"%'");
		end if;
		
		PREPARE sqlquery FROM @ssra;
		EXECUTE sqlquery USING @parent_id;
		
		set @ssra = concat('update user_tmp u set volume = ');
		set @ssra = concat(@ssra ,' (select count(po.id) from product_order po where po.buyer_id = u.id)');

		PREPARE sqlquery FROM @ssra;
		EXECUTE sqlquery;	
   	
		set @ssra = concat('select * from user_tmp ');
		
		if @time_order_by <> '' and @deal_order_by <> '' then 
			set @ssra = concat(@ssra,'order by volume ', @deal_order_by,',create_time ',@time_order_by);			
		elseif @time_order_by = '' and @deal_order_by = '' then
			set @ssra = concat(@ssra,'order by volume desc,create_time desc');
		else 
			if @time_order_by <> '' then
				set @ssra = concat(@ssra,' order by create_time ',@time_order_by);
			end if;
			if @deal_order_by <> '' then
				set @ssra = concat(@ssra,' order by volume ',@deal_order_by);
			end if;	
		end if;
   	
		set @ssra = concat(@ssra, ' limit ?,? ');
		
		PREPARE sqlquery FROM @ssra;
		EXECUTE sqlquery USING @page_index,@size;
		
   		DROP TEMPORARY TABLE IF EXISTS user_tmp;
		end;
	END
DELIMITER ;
```
