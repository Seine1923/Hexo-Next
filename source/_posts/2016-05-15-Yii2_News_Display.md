title: Yii2之News页面展示
date: 2016-05-15 00:00:00
categories: Web开发
tags: [MySQL, PHP, Yii]
description: 本节将详细记录新闻内容从后台添加到前端显示的工作流程<br><br><img src="/images/wamp/displayNews.png" alt="displayNews"><br>详细部署请阅读全文
---

## 1. 后台添加内容

前一节我们通过gii生成了news的model、CRUD后，就可以通过后台添加news内容

![](/images/wamp/createNews.png)

## 2. main.php

	$menuItems = [
        ['label' => '首页', 'url' => ['/site/index']],
	    ['label' => '新闻动态', 'url' => ['/site/newslist']],
	    ['label' => '组织机构', 'url' => ['/site/org']],
	    ['label' => '留言反馈', 'url' => ['/site/contact']],
	    ['label' => '人才招聘', 'url' => ['/site/zhaopin']],
        ['label' => '关于我们', 'url' => ['/site/about']],
    ];

首页（frontend/views/layout/main.php）设置news菜单栏，菜单栏叫做“新闻动态”，点击以后，会跳转到frontend/controllers下的SiteController控制器，找到对应的响应叫actionNewslist

## 3. SiteController->actionNewslist

	public function actionNewslist($offset=0,$limit=10){
        $sql = "SELECT * FROM news WHERE top_level !=0  ORDER BY top_level DESC";
        $topNews = News::findBySql($sql)->all();
        $sql = "SELECT * FROM news WHERE top_level = 0 ORDER BY time DESC LIMIT ".$offset.",".$limit;
        $normalNews = News::findBySql($sql)->all();
        return $this->render('news-list',[
                'topNews' => $topNews,
                'normalNews' => $normalNews
        ]);
    }
	
查询数据库，将数据发送（render）给frontend/views/site/news-list

## 4. news-list.php

获得数据并且与视图结合，形成最终的页面效果展示给用户。

	<?php
		use yii\helpers\Html;
		
		$this->title = '新闻';
		$this->params['breadcrumbs'][] = $this->title;
	?>
	<?=Html::cssFile('@web/css/list.css')?>
	<div id="main">
	    <div class="message-list">
	        <table class="table table-hover">
	            <!--<caption>新闻动态</caption>-->
	            <thead>
	            <tr>
	                <th>标题</th>
	                <th>日期</th>
	            </tr>
	            </thead>
	            <tbody>
	            <?php foreach ($topNews as $line):?>
	            <tr>
	                <td><?=Html::a($line->title,['site/newsControl','id'=>$line->id,'title'=>$line->title])?></td>
	                <td><?=$line->time?></td>
	            </tr>
	            <?php endforeach; ?>
	            <?php foreach ($normalNews as $line):?>
	                <tr>
	                    <td><?=Html::a($line->title,['site/newsControl','id'=>$line->id,'title'=>$line->title])?></td>
	                    <td><?=$line->time?></td>
	                </tr>
	            <?php endforeach; ?>
	            </tbody>
	        </table>
	    </div>
	</div>

## 5. SiteController->actionNewsControl

在news-list.php显示的页面中，通过点击新闻标题首先到相应控制器获得数据后，跳转到news.php，并显示相应文章的内容

    public function actionNewsControl($id){
        if(($model=News::findOne($id)) == null){
            throw new NotFoundHttpException('网页不存在!');
        }else{
            return $this->render('news',['model' => $model]);
        }
    }

## 5. news.php

	<?php
	use yii\helpers\html;
	$this->title = $model->title;
	?>
	<style>
	    .time,.author{
	        display: inline-block;
	        float: left;
	    }
	    .author{
	        margin-left: 950px;
	    }
	    .info{
	        background: #eee;
	        border-radius: 5px;
	    }
	    .news-content{
	        text-indent:2em;
	    }
	</style>
	<acticle class="news">
	    <head class="news-head">
	        <h1 class="news-title"><?=$model->title?></h1>
	    </head>
	    <div class="info">
	        <div class="time">
	            <?=$model->time?>
	        </div>
	        <div class="author">
	            <?=$model->author?>
	        </div>
	        <br>
	    </div>
	    <section class="news-content">
	    <?=\yii\helpers\Markdown::process($model->content)?>
	    </section>
	</acticle>

## 6 效果

![](/images/wamp/newsDetail.png)

可以看到内容已经成果显示，但是可以看到几乎没有样式，为了使文档更富有色彩，可以在后台添加富文本编辑框，对内容样式修改后就可以看到富文本的效果。具体下节演示。

## 7 开启url美化  

在不开启Yii2的enablePrettyUrl模式之前，我们访问新闻列表时的url是这样的：

	http://localhost:8080/web/index.php?r=site%2Fnewslist

来到frontend\config\main.php

	'urlManager' => [
            'enablePrettyUrl' => true,
            'showScriptName' => false,
            'rules' => [
            ],
        ],

在frontend/web/目录下新建一个.htaccess文件，附如下内容：
	RewriteEngine on
	 
	# If a directory or a file exists, use it directly
	RewriteCond %{REQUEST_FILENAME} !-f
	RewriteCond %{REQUEST_FILENAME} !-d
	# Otherwise forward it to index.php
	RewriteRule . index.php

检查apache目录下配置文件httpd.conf下是否开启以下功能：

	LoadModule rewrite_module modules/mod_rewrite.so

以及DocumentRoot下Direction中是否设置了`AllowOverride ALL`

开启后，就可以通过`http://localhost:8080/web/index.php/site/newslist`开访问内容了。
