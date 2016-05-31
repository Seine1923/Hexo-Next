title: Windows7X64_Apache24_MySQL57_PHP7_Yii2配置指南
date: 2016-04-24 00:00:00
categories: Web开发
tags: [Apache, MySQL, PHP, Yii]
description: Windows 7 X64下Apache2.4+MySQL5.7+PHP7+Yii2网站开发配置指南<br><br><img src="/images/wamp/display.png" alt="display"><br>详细部署请阅读全文
---

## 1. 简介

Windows下网站开发配置各组件版本信息如下：

	Windows 7 X64
	Apache 2.4.20
	MySQL 5.7
	PHP 7.0.6
	Yii 2.0.7

相比于[WAMP Server](https://sourceforge.net/projects/wampserver/files/latest/download)这一组集成开发软件，独自安装各个组件对于了解整个网站工作原理会有更大的帮助。

## 2. 安装Apache 2.4.20

1. 从Apache Haus上下载[Apache 2.4 VC14](http://www.apachehaus.com/cgi-bin/download.plx),注意要下载X64版本(httpd-2.4.20-x64-vc14.zip)
2. 下载安装[Visual C++ for Visual Studio 2015 (VC14)](https://www.microsoft.com/en-us/download/details.aspx?id=48145)
3. 解压Apache到C:\Apache24
4. 为了能够通过Apache Monitor(C:\Apache24\bin\ApacheMonitor.exe)来启动和停止Apache服务，需要以管理员身份启动cmd,转到bin目录(`cd "C:\Apache24\bin"`),然后执行`httpd.exe -k install -n "Apache 2.4"`来将Apache2.4安装为一个Windows服务。

## 3. 安装PHP 7.0.6

1. 下载[VC14 x64 Thread Safe](http://windows.php.net/qa/).zip文件
2. 将文件解压到C:/php7

## 4. 配置Apache

1. 打开Apache配置文件：C:\Apache24\conf\httpd.conf
2. 定位到ServerRoot来配置服务根目录为c:/Apache24  
	`ServerRoot "c:/Apache24"`  
	注意是正斜杠"/"，而不是反斜杠"\"  
3. 将Apache的监听端口改为8080，默认的80容易与其他软件冲突，或者选择一个其他空闲的端口
	`Listen 8080`  
4. 定位到DocumentRoot来设置网站目录

		DocumentRoot "C:/Users/Hary/Documents/GitHub/sample/biaozhunhua/advanced/web"  
		<Directory "C:/Users/Hary/Documents/GitHub/sample/biaozhunhua/advanced/web">

5. 复制以下代码到文档最顶端

		AddHandler application/x-httpd-php .php
		AddType application/x-httpd-php .php .html
		LoadModule php7_module "c:/php7/php7apache2_4.dll"
		PHPIniDir "c:/php7"

6. 虚拟主机配置
	1. 为了能使Apache支持多个站点，需要添加虚拟主机
	2. 在Windows的Host文件添加第二个网站的名称与域名`cd C:/windows/system32/drivers/etc/` 
		`127.0.0.1	local_website2`
	3. 在C:\Apache24\conf\httpd.conf文件底端添加如下设置
	
			<VirtualHost 127.0.0.1:8080> 
		      	ServerAdmin webmaster@local_website2 
		     	DocumentRoot "C:/Users/Hary/Documents/GitHub/yii-advanced-app-2.0.7/advanced/frontend/web" 
		      	ServerName local_website2 
		      	ErrorLog logs/local_website2-error.log 
		      	CustomLog logs/local_website2-access_log common 

      			<Directory C:/Users/Hary/Documents/GitHub/yii-advanced-app-2.0.7/advanced/frontend/web> 
					Options Indexes FollowSymLinks Includes ExecCGI
					AllowOverride All
					Order deny,allow
					Require all granted
      			</Directory> 
			</VirtualHost>

	4. 在浏览器输入`http://local_website2:8080/index.php`即可访问新站点。
	5. 若访问时出现没有权限访问，则是由于没有在`<Directory></Directory>`之间加上`Require all granted`

## 5. 配置PHP

1. 将`C:\php7\php.ini-development`文件重命名为`php.ini`，做好备份。
2. 在`php.ini`中找到`extension_dir`，添加扩展插件目录  
	`extension_dir = "C:/php7/ext"`
3. 若出现`Fatal error: Call to undefined function mb_strlen()`错误，则是由于没有在`php.ini`中配置`extension_dir`

## 6. 测试Apache和PHP

1. 在网站目录下新建一个`phpinfo.php`文件，并附上如下代码

		<?php 
			phpinfo();  
		?>

2. 在浏览器上访问`http://localhost:8080/phpinfo.php`即可查看到PHP配置信息

## 7. Yii2

1. [Yii2.0中文开发文档](http://www.yiifans.com/yii2/guide/)
2. [Composer](https://getcomposer.org/):Windows版下载Composer-Setup.exe安装，并且会自动设置环境变量。
2. 去官网下载一个[Yii的高级模版](http://www.yiiframework.com/download/)
	![](/images/wamp/yiidownload.png)
3. 以管理员方式解压Yii的高级模板到web可访问目录，文件名advanced不做改动
4. 切换到advanced目录如图开始配置
	![](/images/wamp/yiiInit1.png)  
	![](/images/wamp/yiiInit2.png)
5. 在Windows下的Host文件下添加：

		127.0.0.1	mysite
6. 在Apache下的httpd.conf添加：

		<VirtualHost 127.0.0.1:8080> 
	      ServerAdmin webmaster@mysite 
	      DocumentRoot "C:/Users/Hary/Documents/GitHub/mysite/advanced/frontend/web" 
	      ServerName mysite 
	      ErrorLog logs/mysite-error.log 
	      CustomLog logs/mysite-access_log common 
	         
	      <Directory C:/Users/Hary/Documents/GitHub/mysite/advanced/frontend/web> 
				Options Indexes FollowSymLinks Includes ExecCGI
				AllowOverride All
				Order deny,allow
				Require all granted
	      </Directory> 
		</VirtualHost>
7. 访问测试`http://mysite:8080/index.php`
	![](/images/wamp/yii.png)  
8. 在安装好MySQL后，连接数据库设置，在mysite\advanced\common\config下设置main-local.php如下：
	![](/images/wamp/dbconnect.png)
9. 在MySQL WorkBench下创建数据库

		create database mysitedb
10. 创建user表（menu表可参考如下），然后就可以注册管理员了

		CREATE TABLE `user` (
			`id` int(11) NOT NULL AUTO_INCREMENT COMMENT '自增ID',  
			`username` varchar(255) NOT NULL COMMENT '用户名',  
			`auth_key` varchar(32) NOT NULL COMMENT '自动登录key',  
			`password_hash` varchar(255) NOT NULL COMMENT '加密密码',  
			`password_reset_token` varchar(255) DEFAULT NULL COMMENT '重置密码token',  
			`email` varchar(255) NOT NULL COMMENT '邮箱',  
			`role` smallint(6) NOT NULL DEFAULT '10' COMMENT '角色等级',  
			`status` smallint(6) NOT NULL DEFAULT '10' COMMENT '状态',  
			`created_at` int(11) NOT NULL COMMENT '创建时间',  
			`updated_at` int(11) NOT NULL COMMENT '更新时间',  
			PRIMARY KEY (`id`)
		) ENGINE=InnoDB AUTO_INCREMENT=0 DEFAULT CHARSET=utf8 COMMENT='用户表';
11. 创建news表以及插入测试数据
		
		CREATE TABLE `news` (
		  `id` int(11) NOT NULL AUTO_INCREMENT,
		  `title` varchar(255) NOT NULL,
		  `content` text NOT NULL,
		  `type` int(11) NOT NULL,
		  `time` date DEFAULT NULL,
		  `author` varchar(40) NOT NULL,
		  `top_level` int(11) DEFAULT '0',
		  PRIMARY KEY (`id`)
		) ENGINE=InnoDB AUTO_INCREMENT=9 DEFAULT CHARSET=utf8

		insert into news(id, title,content,type,time,author,top_level) value(9801, 'title display test','content display test', 4, '2016-04-26', 'Hary',1)
12. 使用gii:`http://mysite2:8080/frontend/web/index.php?r=gii`,由数据库中的news表创建Model,设置如下后点击Preview和Generate。  
	![](/images/wamp/modelGenerator.png)  
	![](/images/wamp/modelGeneratorResult.png)  
12. 有了数据模型以后，生成相应的CRUD(Create, Read, Update, Delete)操作：
	![](/images/wamp/CRUDGenerator.png)
	![](/images/wamp/CRUDPreview.png)
	![](/images/wamp/CRUDfinish.png)
13. 在backend/views/layouts/main.php中，添加news菜单代码如下：

		 $menuItems = [
	        ['label' => 'Home', 'url' => ['/site/index']],
	        ['label' => 'news', 'url' => ['/news/index']],
	    ];

14. 访问`http://mysite2:8080/backend/web/index.php`就可以看到news菜单并点击访问
	![](/images/wamp/newsprofile.png)
	然后就可以对内容进行增删查改了。
15. backend/views/layouts下文件介绍：
	
		main.php：首页抬头（菜单）展示
16. backend/views/news下文件介绍：  

		_form.php：新建/修改新闻的页面
		_search.php:搜索响应页面
		create.php：点击新建新闻的响应页面
		index.php：新闻后台的主页面，为了使页面更简洁，可将代码'content:ntext'隐去，以不显示新闻内容
		update.php：点击修改新闻的响应页面
		view.php：点击查看新闻的响应页面    
17. backend/views/site下文件介绍：

		error.php：出错后页面展示
		index.php：首页内容展示
		login.php：登录页面展示      

## 8. 安装MySQL

1. 下载[MySQL Community Server (GPL)](http://dev.mysql.com/downloads/),选择Windows (X86,32bit), MySQL Install MSI安装包(mysql-installer-community-5.7.12.0.msi)。
2. 默认安装即可，安装时需要设置root密码。

## 9. PHP开发工具

1. [phpStorm](https://www.jetbrains.com/phpstorm/download/)是写PHP最好的IDE之一
2. [注册码](http://idea.qinxi1992.cn/)