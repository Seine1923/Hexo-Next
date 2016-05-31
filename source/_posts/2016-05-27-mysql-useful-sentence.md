title: MySQL常用命令
date: 2016-05-27 00:00:00
categories: Web开发
tags: [MySQL, PHP, Yii2]

---

## 1. 创建数据库

	create database yii2basic

## 2. 修改字段值

	UPDATE 表名 SET 字段名=REPLACE(字段名,'原字符串','替换的字符串') WHERE 已知的字段名 LIKE '%原字符串%'  
	UPDATE 表名 SEt 字段名='字符串' WHERE 字段='替换的字符串' AND 条件;

	update user set groups=0 where id=1

## 3. 关闭safe mode

	SET SQL_SAFE_UPDATES=0; 

## 4. 删除表中指定内容

	delete from auth_assignment where user_id=2

## 5. 删除表中所有内容

	delete from auth_assignment