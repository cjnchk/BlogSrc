layout: post
title: "yii2入口文件：Web Applications 和 Console Applications"
date: 2015-07-21 21:40:35
categories: [yii2]
tags: [yii2]
keywords: yii2, 入口文件, Web, Console
description: yii2入口文件配置
comments: true
photos:
-
---
　　yii框架中通常包含两类入口文件，一类是针对用户的web入口，一类是针对命令行的控制台入口。要注意两个入口的一致性，否则可能在命令行层面造成相关全局常量或函数的引用缺失。

## **入口文件**

- 通常用来定义全局常量：如debug调用，当前运行环境
```
    defined('YII_DEBUG') or define('YIII_DEBUG', true);
    defined('YII_DEV') or define('YII_DEV', 'dev');
```
- 注册自动加载函数（composer autoloader）
```
    require(__DIR__ . '/../vender/autoload.php');
```
- 加载Yii类文件
```
    require(__DIR__ . '/../vender/yiisoft/yii2/yii.php');
```
- 加载应用配置
```
    $config = require(__DIR__ . '/../config/web.php');
```
- 调用`yii\base\Application::run()`处理请求
```
    $application = new yii\web\Application($config);
    $application->run();
```
<!--more-->

** * 大致形式如下 * ** (* 其中部分内容会在后面深入分析 *)

{% codeblock lang:php %}
    <?php
    defined('YII_DEBUG') or define('YII_DEBUG', true);
    defined('YII_ENV') or define('YII_ENV', 'dev');

    require(__DIR__ . '/../../vendor/autoload.php');
    require(__DIR__ . '/../../vendor/yiisoft/yii2/Yii.php');
    require(__DIR__ . '/../../common/config/bootstrap.php');
    require(__DIR__ . '/../config/bootstrap.php');

    $config = yii\helpers\ArrayHelper::merge(
        require(__DIR__ . '/../../common/config/main.php'),
        require(__DIR__ . '/../../common/config/main-local.php'),
        require(__DIR__ . '/../config/main.php'),
        require(__DIR__ . '/../config/main-local.php')
    );

    $application = new yii\web\Application($config);
    $application->run();
{% endcodeblock %}

## ** 关于Application **

当入口文件创建应用时，会相应的引入相关文件，加载各种配置。下面会介绍一些相关应用的配置，及主要属性。
其中主要部分是上文实例中载入的的**main.php**所包含的相关属性。
{% codeblock lang:php %}
    <?php
    $params = array_merge(
        require(__DIR__ . '/../../common/config/params.php'),
        require(__DIR__ . '/../../common/config/params-local.php'),
        require(__DIR__ . '/params.php'),
        require(__DIR__ . '/params-local.php')
    );

    return [
        'id' => 'app-frontend',
        'basePath' => dirname(__DIR__),
        'bootstrap' => ['log'],
        'controllerNamespace' => 'frontend\controllers',
        'components' => [
            'urlManager'=>[
                'enablePrettyUrl'=>true,
                'showScriptName'=>false,
                'enableStrictParsing'=>false,
                'rules'=>[
                    '<controller:<\w+>>/<action:<\w+>>'=>'<controller>/<action>'
                ]
            ],
            'user' => [
                'identityClass' => 'common\models\User',
                'enableAutoLogin' => true,
            ],
            'log' => [
                'traceLevel' => YII_DEBUG ? 3 : 0,
                'targets' => [
                    [
                        'class' => 'yii\log\FileTarget',
                        'levels' => ['error', 'warning'],
                    ],
                ],
            ],
            'errorHandler' => [
                'errorAction' => 'site/error',
            ],
        ],
        'params' => $params,
    ];
{% endcodeblock%}
依据上述文件，下面将分别介绍其**必备属性**、**很重要的属性**、**不重要，但也很有用的属性**、**应用级事件**、**应用生命周期图**。

### ** 必备属性 **

** *id* **

如上所示，这里的id（最好由字母数字组成）具有绝对唯一性，主要用来区分不同应用。

** *basePath* **

basePath是指当前应用所在的根目录，可以通过文件目录（/path/to/path1）或别名形式。这里着重提一下别名形式，Yii里预定义了一个别名来代指根目录`@app`，可以用它来派生其他目录，如记录程序执行日志的`@app/runtime`文件目录。

### ** 很重要的属性(这里只介绍几个比较有意思的) **

** *aliases* **

此属性允许我们通过配置文件以array的形式定义一个alias集合。避免在应用的到处调用` Yii::setAlias() `，还方便统一管理。
```
    [
        'aliases' => [
            '@name1' => 'path/to/path1',
            '@name2' => 'path/to/path2',
        ],
    ]
```
** *catchAll* **

利用这个属性，我们可以在哪天需要关闭系统，但又要给用户一个页面告知用户关闭原因时，为所有请求形成一个统一的入口，指向我们的告用户界面。
```
    [
        'catchAll' => [
            'offline/notice',
            'param1' => 'value1',
            'param2' => 'value2',
        ],
    ]
```
** *components* **

非常非常重要，主要用来注册一些应用组件，放在**Application Components**篇里详细讲解。

** *controllerNamespace* **

自从php5.3引入类似java，c++，python的命名空间的概念后，感觉真的是好用了很多，只是为了向下兼容，反斜杠`/`的应用可能让人觉得有些怪诞。

回归主题，这个属性规定了controller类的默认命名空间，通过他亦可以解析子级目录下的controller，例如：某个controller ID为`admin/post`， 那么解析为 `app\controllers\admin\PostController`。

** *modules* **

这个属性指定了程序包含的模块（modules:一个位于已有MVC应用内，亦包含完整MVC结构的模块）。在**modules**章节详细分析。
```
    [
        'modules' => [
            // a "booking" module specified with the module class
            'booking' => 'app\modules\booking\BookingModule',

            // a "comment" module specified with a configuration array
            'comment' => [
                'class' => 'app\modules\comment\CommentModule',
                'db' => 'db',
            ],
        ],
    ]
```
** *timeZone* **

当配置这个属性后，应用实际上会调用PHP的内置函数`date_default_timezone_set()`来设置时区。
```
    [
        'timeZone' => 'zh/Shanghai',
    ]
```
### ** 不重要，但也很有用的属性 **

[详见文档](http://www.yiiframework.com/doc-2.0/guide-structure-applications.html)

### ** 应用级事件（全局的，顶层的，不局限于某处） **

在一个请求处理周期内，应用会触发若干事件，其中会有下面四个应用级事件：

- **EVENT_BEFORE_REQUEST** (应用处理请求之前触发的事件)
```
    [
        'on beforeRequest' => function ($event) {
            // ...
        },
    ]
```
- **EVENT_AFTER_REQUEST** (应用处理请求完成后，响应发送之前，触发的事件)
```
    [
        'on afterRequest' => function ($event) {
            // ...
        },
    ]
```
- **EVENT_BEFORE_REQUEST** (任何一个controller 的 action 运行之前，其参数`$event`是`yii\base\ActionEvent`的一个实例)
```
    [
        'on beforeAction' => function ($event) {
            if (some condition) {
                $event->isValid = false;
            } else {
            }
        },
    ]
```
- **EVENT_AFTER_ACTION** (任何一个controller 的 action 运行完成之后，其参数`$event`是`yii\base\ActionEvent`的一个实例)
```
    [
        'on afterAction' => function ($event) {
            if (some condition) {
                $event->isValid = false;
            } else {
            }
        },
    ]
```
### ** 应用生命周期图（Application Lifecycle） **

![Application Lifecycle](http://www.yiiframework.com/doc-2.0/images/application-lifecycle.png "应用生命周期图")

