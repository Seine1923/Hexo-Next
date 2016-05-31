title: 基于Yii2高级模板的配置与开发
date: 2016-05-25 00:00:00
categories: Web开发
tags: [MySQL, PHP, Yii2]

---

## 1. 安装高级版本

[Windows下通过Composer命令安装Yii2 Advanced框架](http://seine1923.github.io/2016/05/18/2016-05-19-yii2-advance-install-by-composer/)

## 2. 准备数据库

	create database mysitedb

/common/config/main-local.php 

	dbname=mysitedb
	password='your-mysql-root-password'

使用数据库migration来初始化项目数据，首先设置用户管理表
	
	D:\mysite>yii migrate
	Yii Migration Tool (based on Yii v2.0.8)
	
	Creating migration history table "migration"...Done.
	Total 1 new migration to be applied:
	        m130524_201442_init
	
	Apply the above migration? (yes|no) [no]:yes
	*** applying m130524_201442_init
	    > create table {{%user}} ... done (time: 0.312s)
	*** applied m130524_201442_init (time: 0.367s)
	
	
	1 migration was applied.
	
	Migrated up successfully.
	
	D:\mysite>    

## 3. 用户管理

1. 接收确认邮件

	激活SwiftMailer SMTP功能

	[Mailtrap.io](https://mailtrap.io/)

	/common/config/main-local.php

		'mailer' => [
            'class' => 'yii\swiftmailer\Mailer',
            'viewPath' => '@common/mail',
            'useFileTransport' => false,
            'transport' => [
                'class' => 'Swift_SmtpTransport',
                'host' => 'mailtrap.io',
                'username' => '85**********5c',
                'password' => '62**********e6',
                'port' => '2525',
                'encryption' => 'tls',
            ],
        ],

	注册和登录

	当你忘记密码时就可以通过邮件找回

