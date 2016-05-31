title: Yii2之后台集成富文本编辑器
date: 2016-05-17 00:00:00
categories: Web开发
tags: [MySQL, PHP, Yii]
description: 本节将详细记录Yii2后台集成富文本编辑器的步骤<br><br><img src="/images/wamp/pictureNews.png" alt="displayNews"><br>详细部署请阅读全文
---

上一节我们了解在Yii2后台添加新闻内容到数据库，再从前端显示的流程。但是如果只是使用textarea，文本内容太单调了，因此还需要集成一个富文本编辑器，这里选择功能强大的Redactor。

## 1. 安装Redactor

通过composer安装：cmd->cd C:\Users\Hary\Documents\GitHub\sample\biaozhunhua\advanced>  
输入命令：

	composer require --prefer-dist yiidoc/yii2-redactor "*"

![](/images/wamp/installRedactor.png)

## 2. backend\config\main.php

在modules下添加:

	'redactor' => 'yii\redactor\RedactorModule',

整个代码如下：

	'params' => $params,
    'modules' => [
        'redactor' => 'yii\redactor\RedactorModule',
    ],

## 3. backend\views\news\_form.php

将`<?= $form->field($model, 'content')->textarea(['rows' => 6]) ?>`替换成`<?= $form->field($model, 'content')->widget(\yii\redactor\widgets\Redactor::className()) ?>`

## 4. 效果

![](/images/wamp/richText.png)

## 5. 图片支持

首先需要在web/目录下创建一个uploads/目录，这是Redactor默认的上传图片的存放目录；然后我们还需要修改一下backend\config\main.php这个文件中的Redactor的配置：

    'modules' => [
        'redactor' => [
            'class' => 'yii\redactor\RedactorModule',
            'uploadDir' => '@backend/web/uploads',
            'imageAllowExtensions'=>['jpg','png','gif']
        ],
    ],

uploadUrl还不确定怎么设置，让其默认就好了。我们这里指定了上传图片的类型，演示时只支持jpg，png 和gif这三种，最后在backend\views\news\_form.php中进行相应的设置：

	 <?= $form->field($model, 'content')->widget(\yii\redactor\widgets\Redactor::className(), [
        'clientOptions' => [
            'imageManagerJson' => ['/redactor/upload/image-json'],
            'imageUpload' => ['/redactor/upload/image'],
            'fileUpload' => ['/redactor/upload/file'],
            'lang' => 'zh_cn',
            'plugins' => ['clips', 'fontcolor','imagemanager']
        ]
    ])?>

clientOptions置了图片管理和上传，文件上传，显示语言，和一些小插件：字体颜色，字体背景色等。图片和文件的上传都是用的官方默认的上传配置，更多的配置和文档[点这里](https://github.com/yiidoc/yii2-redactor)

[文件上传安全配置](https://digwp.com/2012/09/secure-media-uploads/)

## 6. 演示

![](/images/wamp/redactor.gif)

## 7. 前提

1. 保证你的php支持fileinfo扩展。打开php.ini文件去掉extension=php_fileinfo.dll前面的分号。
2. 若项目在C盘，则会因为导致权限原因图片上传不成功。

## 8. 备忘录

1. 可通过查看Apache服务器信息调试程序，查找问题，具体访问@Apache24\logs\access.log,查看每次提交信息时服务器状态

	![](/images/wamp/accesslog.png)  

2. 屏幕录像工具[LICEcap](http://licecap.en.softonic.com/)