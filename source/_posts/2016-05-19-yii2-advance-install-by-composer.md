title: Windows下通过Composer命令安装Yii2 Advanced框架 
date: 2016-05-18 00:00:00
categories: Web开发
tags: [MySQL, PHP, Yii2]

---

## 1. 安装Composer

[GetComposer](https://getcomposer.org/download/): Composer-Setup.exe，安装后自动会设置环境变量。

## 2. 更新

composer self-update 

## 3. 安装Yii2高级版

切换到D:\目录下，按住Shift同时右键，选择“在此处打开命令窗口”，输入以下命令安装：

1. 安装 Composer asset plugin，它是通过 Composer 管理 bower 和 npm 包所必须的，此命令全局生效，一劳永逸。

		composer global require "fxp/composer-asset-plugin:~1.1.1"

2. 安装基础版命令（仅供参考）

		composer create-project --prefer-dist yiisoft/yii2-app-basic basic

3. 安装高级版

		composer create-project --prefer-dist yiisoft/yii2-app-advanced dongxublog

	![](/images/wamp/dongxublog-install.png)  
	![](/images/wamp/dongxublog-file.png)

4. 初始化项目

	切换到项目目录，输入命令：

		php init  

 	![](/images/wamp/dongxublog-init.png)

5. 检验

	首先将项目目录设为Web访问目录，然后在浏览器中输入：
	
		http://dongxublog:8080/frontend/web/index.php

	![](/images/wamp/dongxublog-index.png)

		http://dongxublog:8080/backend/web/index.php?r=site%2Flogin
	![](/images/wamp/dongxublog-backend.png)
		