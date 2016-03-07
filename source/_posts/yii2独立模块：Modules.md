layout: post
title: "yii2独立模块：Modules"
date: 2015-08-26 21:57:16
categories: [yii2]
tags: [独立模块]
keywords:
description:
comments: true
photos:
-
---
Modules是一个由模型层、视图层、控制器层构成的自包含模块，另外还支持组件（components），终端用户可以直接访问modules的控制器。所以，modules会被视为一个小型的应用，模块却别于正常应用的是，其不能独立存在，而必须隶属于应用的一部分。

## **创建Modules**

模块通常位于根目录，在这个目录内包含有子目录，如`controllers`、`models`、`views`，形如一个正常的应用，下面是一个模块的例子：

```
    forum/
        Module.php                   the module class file
        controllers/                 containing controller class files
            DefaultController.php    the default controller class file
        models/                      containing model class files
        views/                       containing controller view and layout files
            layouts/                 containing layout view files
            default/                 containing view files for DefaultController
                index.php            the index view file
```
<!--more-->
## **模块类（Modules Classes）**

每个模块都应该有一个继承自`yii\base\Module`的唯一module类。该类应当为模块根目录下，并且能够自动加载。当模块被访问时，其对应的模块类会生成一个实例。如同应用实例，模块实例为模块内的代码提供共享配置及组件。

下面是一个module类的例子：

```
    namespace app\modules\forum;

    class Module extends \yii\base\Module
    {
        public function init()
        {
            parent::init();

            $this->params['foo'] = 'bar';
            // ...  other initialization code ...
        }
    }
```
如果`init()`方法内包很多初始化代码，就可以将他们保存在一个单独的配置文件里，然后在`init()`里加载这个配置文件。

```
    public function init()
    {
        parent::init();
        // initialize the module with the configuration loaded from config.php
        \Yii::configure($this, require(__DIR__ . '/config.php'));
    }
```
配置文件`config.php`通常包含以下内容，类似于应用的配置：

{% codeblock lang:php %}
     <?php
        return [
            'components' => [
                // list of component configurations
            ],
            'params' => [
                // list of parameters
            ],
        ];
    ?>
{% endcodeblock %}

## **使用模块**
为了能够使用模块，需要将其列入应用配置modules属性内，下面是一个在应用配置文件中使用`form`模块的例子：
```
    [
        'modules' => [
            'forum' => [
                'class' => 'app\modules\forum\Module',
                // ... other configurations for the module ...
            ],
        ],
    ]
```
`modules`属性是一个包含模块配置的数组，数组的key是标识模块的唯一ID，值是用来的创建模块的配置。

## **路由（routes）**
如同访问应用中的控制器一样，路由通常用来定位模块总的控制器。模块中的控制路由由`module ID`、`controller ID`、`action ID`依次连接组成，例如：`form/post/index`。如果只写`module ID`，那么将会访问名为`default`的默认控制器。

## **模块访问**
在一个模块内，我们可能经常需要获得模块的实例，以便访问其ID、参数、组件等。
获取实例可以通过以下方式：
```
$module = MyModuleClass::getInstance();

// get the child module whose ID is "forum"
$module = \Yii::$app->getModule('forum');

// get the module to which the currently requested controller belongs
$module = \Yii::$app->controller->module;
```
一旦获得实例，便可访问其参数：
`$maxPostCount = $module->params['maxPostCount'];`

## **模块启动引导（Bootstrapping Modules）**

有些模块我们可能需要针对任何请求，例如debug模块。因此，应当将模块ID放入应用的`bootstrap`属性。

下面的配置能够保证debug模块总会启动：
```
    [
        'bootstrap' => [
            'debug',
        ],

        'modules' => [
            'debug' => 'yii\debug\Module',
        ],
    ]
```

## **模块嵌套**
模块嵌套时，需要在其父模块作声明，例如：
```
    namespace app\modules\forum;

    class Module extends \yii\base\Module
    {
        public function init()
        {
            parent::init();

            $this->modules = [
                'admin' => [
                    // you should consider using a shorter namespace here!
                    'class' => 'app\modules\forum\modules\admin\Module',
                ],
            ];
        }
    }
```
访问嵌套模块的路由规则应当是，包含其所有的祖先模块ID，外加controller ID、action ID，例如：
`forum/admin/dashboard/index`

## **最佳实践**

- 模块通常被用于一些复杂的应用中，其某些特性可以独立成组，构成一个模块，每个模块可以有指定的开发人员或team开发和维护。

- 模块也可用作代码复用，尤其针对可以独立成块的系统特性，如用户管理、评论管理等。
