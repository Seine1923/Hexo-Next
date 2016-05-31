title: 基于Yii2高级模板DbManager	的RBAC配置与开发
date: 2016-05-26 00:00:00
categories: Web开发
tags: [MySQL, PHP, Yii2]

---

## 1. 配置authManager

common/config/main.php

	return [
	    // ...
	    'components' => [
	        'authManager' => [
	            'class' => 'yii\rbac\DbManager',
	        ],
	        // ...
	    ],
	];

## 2. 创建数据表

`itemTable`、`itemChildTable`、`assignmentTable`、`ruleTable`

	yii migrate --migrationPath=@yii/rbac/migrations

	D:\yii2-project>yii migrate --migrationPath=@yii/rbac/migrations
	Yii Migration Tool (based on Yii v2.0.8)
	
	Total 1 new migration to be applied:
	        m140506_102106_rbac_init
	
	Apply the above migration? (yes|no) [no]:yes
	*** applying m140506_102106_rbac_init
	    > create table {{%auth_rule}} ... done (time: 0.306s)
	    > create table {{%auth_item}} ... done (time: 0.342s)
	    > create index idx-auth_item-type on {{%auth_item}} (type) ... done (time: 0
	.364s)
	    > create table {{%auth_item_child}} ... done (time: 0.252s)
	    > create table {{%auth_assignment}} ... done (time: 0.266s)
	*** applied m140506_102106_rbac_init (time: 1.581s)
	
	
	1 migration was applied.
	
	Migrated up successfully.
	
	D:\yii2-project>

然后就可以通过\Yii::$app->authManager访问authManager了。

## 3. 创建认证数据

目的：  
1. 确定一个角色及其权限  
2. 建立角色与权限的关系  
3. 建立规则  
4. 将角色与权限关联起来  
5. 分配规则给用户  

在console/controllers下新建RbacController，内容如下：

	<?php
	namespace console\controllers;
	
	use Yii;
	use yii\console\Controller;

	class RbacController extends Controller
	{
	    public function actionInit()
	    {
	        $auth = Yii::$app->authManager;
	
	        // add "createPost" permission
	        $createPost = $auth->createPermission('createPost');
	        $createPost->description = 'Create a post';
	        $auth->add($createPost);
	
	        // add "updatePost" permission
	        $updatePost = $auth->createPermission('updatePost');
	        $updatePost->description = 'Update post';
	        $auth->add($updatePost);
	
	        // add "author" role and give this role the "createPost" permission
	        $author = $auth->createRole('author');
	        $auth->add($author);
	        $auth->addChild($author, $createPost);
	
	        // add "admin" role and give this role the "updatePost" permission
	        // as well as the permissions of the "author" role
	        $admin = $auth->createRole('admin');
	        $auth->add($admin);
	        $auth->addChild($admin, $updatePost);
	        $auth->addChild($admin, $author);
	
	        // Assign roles to users. 1 and 2 are IDs returned by IdentityInterface::getId()
	        // usually implemented in your User model.
	        $auth->assign($author, 2);
	        $auth->assign($admin, 1);
	    }
	}

执行命令：`yii rbac/init`

获得权限结构如下：

![](\images\wamp\rbac-hierarchy-1.png)

作者可以新建文章，管理员可以修改文章并且拥有作者所有的权限。

通过在`frontend\models\SignupForm::signup()`可以给新用户注册角色

	public function signup()
	{
	    if ($this->validate()) {
	        $user = new User();
	        $user->username = $this->username;
	        $user->email = $this->email;
	        $user->setPassword($this->password);
	        $user->generateAuthKey();
	        $user->save(false);
	
	        // the following three lines were added:
	        $auth = Yii::$app->authManager;
	        $authorRole = $auth->getRole('author');
	        $auth->assign($authorRole, $user->getId());
	
	        return $user;
	    }
	
	    return null;
	}

但为了方便管理，我们需要通过authManager提供的API创建一个管理员面板

1. 使用rules

rules可以增加对角色和权限的约束，它继承自yii\rbac\Rule，必须有execute()方法，之前的权限结构，作者不能修改自已的文章，现在要改进它，使其能修改自己的文章。首先我们设定一个rule来验证当前作者就是该文章的作者：（console\models\下新建AuthorRule.php）

	namespace app\rbac;
	
	use yii\rbac\Rule;
	
	/**
	 * Checks if authorID matches user passed via params
	 */
	class AuthorRule extends Rule
	{
	    public $name = 'isAuthor';
	
	    /**
	     * @param string|integer $user the user ID.
	     * @param Item $item the role or permission that this rule is associated with
	     * @param array $params parameters passed to ManagerInterface::checkAccess().
	     * @return boolean a value indicating whether the rule permits the role or permission it is associated with.
	     */
	    public function execute($user, $item, $params)
	    {
	        return isset($params['post']) ? $params['post']->createdBy == $user : false;
	    }
	}

以上检查文章是否是该作者创建，接下来创建一个特殊的权限`updateOwnPost`

	$auth = Yii::$app->authManager;
	
	// add the rule
	$rule = new \app\rbac\AuthorRule;
	$auth->add($rule);
	
	// add the "updateOwnPost" permission and associate the rule with it.
	$updateOwnPost = $auth->createPermission('updateOwnPost');
	$updateOwnPost->description = 'Update own post';
	$updateOwnPost->ruleName = $rule->name;
	$auth->add($updateOwnPost);
	
	// "updateOwnPost" will be used from "updatePost"
	$auth->addChild($updateOwnPost, $updatePost);
	
	// allow "author" to update their own posts
	$auth->addChild($author, $updateOwnPost);

得到一个新的权限结构

![](\images\wamp\rbac-hierarchy-2.png)

2. 访问权限检查

前面已经将认证数据准备好了，访问权限检查只需要调用yii\rbac\ManagerInterface::checkAccess()方法，大部分访问权限检查是基于当前用户，因此yii提供了一个简略方法yii\web\User::can()：

	if (\Yii::$app->user->can('createPost')) {
	    // create post
	}

比如当前用户是Jane,ID=1,她的权限流程如下：

![](\images\wamp\rbac-access-check-1.png)

为了检查一个用户是否能够更新一篇文章，根据之前设置的AuthorRule，还需要额外传递一个参数：

	if (\Yii::$app->user->can('updatePost', ['post' => $post])) {
	    // update post
	}

比如当前用户是John，他的权限检查如下：

![](\images\wamp\rbac-access-check-2.png)

为了通过访问权限检查，`AuthorRule`会在`execute()`方法后返回一个`true`值，该方法`can()`方法中接收参数`$params`，验证`['post' => $post]`

而对于管理员来说，直接可以获得权限

![](\images\wamp\rbac-access-check-3.png)

3. 使用默认的角色

一个默认的角色是默认分配给所有的用户，也就是所谓的分组，组中的权限默认分配给所有属于组的用户。比如user表中有一字段为group,我们使用值1来代表管理组，2代表作者组。我们使用两个RBAC角色分别是admin和author分别代表两个组的权限，首先设置如下的RBAC数据：

	namespace app\rbac;
	
	use Yii;
	use yii\rbac\Rule;
	
	/**
	 * Checks if user group matches
	 */
	class UserGroupRule extends Rule
	{
	    public $name = 'userGroup';
	
	    public function execute($user, $item, $params)
	    {
	        if (!Yii::$app->user->isGuest) {
	            $group = Yii::$app->user->identity->group;
	            if ($item->name === 'admin') {
	                return $group == 1;
	            } elseif ($item->name === 'author') {
	                return $group == 1 || $group == 2;
	            }
	        }
	        return false;
	    }
	}
	
	$auth = Yii::$app->authManager;
	
	$rule = new \app\rbac\UserGroupRule;
	$auth->add($rule);
	
	$author = $auth->createRole('author');
	$author->ruleName = $rule->name;
	$auth->add($author);
	// ... add permissions as children of $author ...
	
	$admin = $auth->createRole('admin');
	$admin->ruleName = $rule->name;
	$auth->add($admin);
	$auth->addChild($admin, $author);
	// ... add permissions as children of $admin ...

接下来配置authManager

	return [
	    // ...
	    'components' => [
	        'authManager' => [
	            'class' => 'yii\rbac\PhpManager',
	            'defaultRoles' => ['admin', 'author'],
	        ],
	        // ...
	    ],
	];

基于此，在访问权限检查时，如果如果字段值`group=1`，则会分配一个admin权限，如果字段值`group=2`，则会分配一个author权限。