layout: post
title: "yii2MVC应用"
date: 2015-07-26 12:05:49
categories: [yii2]
tags: [php, yii2, MVC]
keywords: yii2, MVC
description: yii2中的MVC
comments: true
photos:
-
---
MVC是应用中常用的组织架构，对于一些基础的东西这里不做赘述，只介绍一些不是很常用的但是颇有裨益的内容，希望在以后的开发中能够加以运用。

## **Controller**

controller主要负责接收请求数据并转发给model层处理，然后将返回值交给view层展现给用户。

### **独立actions(standalone actions)**

satandalone actions 是指独立于controller的单独的一个action，一般负责一个独立功能，这个功能会被多方复用，比如获取验证码的captcha，捕获错误error。此类action需继承自`yii\base\Action`或是其子类，并实现其`run()`方法。
<!--more-->
例如：
{% codeblock lang:php %}
    <?php
    namespace frontend\controllers;

    use yii\base\Action;

    class HelloWorldAction extends Action
    {
        public function run()
        {
            return 'HelloWorld';
        }
    }
{% endcodeblock %}

通过如下方式在`siteController`里调用：

{% codeblock lang:php %}
     /**
     * @inheritdoc
     */
    public function actions()
    {
        return [
            'helloWorld' => [
                'class' => 'frontend\controllers\HelloWorldAction',
            ],
        ];
    }
{% endcodeblock %}

通过如下方式访问：

    http://www.yii2test.com/site/helloWorld

返回：HelloWorld

### **最佳设计**

在一个设计良好的应用中，controllers通长是比较简洁的，每个action仅包含少量代码。如果你的controller相当复杂，通常意味着你需要重构这部分代码，将一部分代码转移到其他类中。
下面是一些针对controllers的好的建议：
- 接收请求数据
- 调用models或其他服务组件来处理请求数据
- 通过views展示数据
- 不在controllers处理请求数据，应当交由model层处理
- 避免嵌入html或其他表述型代码，这部分应当放到view层

## **Models**

### **场景(Scenarios)**

scenarios特性主要用在验证（validation）和属性批量分配（massive assignment）上，当然也可用在其他方面，根据不同的scenario采用不同的属性标签（attribute labels）。

可以通过一下两种方式为model设定scenario：

{% codeblock lang:php %}
    // 1. scenario is set as a property
    $model = new User;
    $model->scenario = User::SCENARIO_LOGIN;

    //2. scenario is set throught configuration
    $model = new User(['scenario' => User::SCENARIO_LOGIN]);
{% endcodeblock %}

默认情况下，scenario是依赖于model里定义的validation rules的，如：

{% codeblock lang:php %}
    public function rules()
    {
        return [
            // username, email and password are all required in "register" scenario
            [['username', 'email', 'password'], 'required', 'on' => self::SCENARIO_REGISTER],

            // username and password are required in "login" scenario
            [['username', 'password'], 'required', 'on' => self::SCENARIO_LOGIN],
        ];
    }
{% endcodeblock %}

但是，我们可通过重写`yii\base\Model::scenarios()`方法来自定义scenario：

{% codeblock lang:php %}
    namespace app\models;

    use yii\db\ActiveRecord;

    class User extends ActiveRecord
    {
        const SCENARIO_LOGIN = 'login';
        const SCENARIO_REGISTER = 'register';

        public function scenarios()
        {
            return [
                self::SCENARIO_LOGIN => ['username', 'password'],
                self::SCENARIO_REGISTER => ['username', 'email', 'password'],
            ];
        }
    }
{% endcodeblock %}

默认的`scenarios()`接口，会返回所有validation rules里声明的所有scenarios，当重写`scenarios()`时，如果你想添加新的scenarios到默认的场景中，可以遵循如下形式：

{% codeblock lang:php %}
    namespace app\models;

    use yii\db\ActiveRecord;

    class User extends ActiveRecord
    {
        const SCENARIO_LOGIN = 'login';
        const SCENARIO_REGISTER = 'register';

        public function scenarios()
        {
            $scenarios = parent::scenarios();
            $scenarios[self::SCENARIO_LOGIN] = ['username', 'password'];
            $scenarios[self::SCENARIO_REGISTER] = ['username', 'email', 'password'];
            return $scenarios;
        }
    }
{% endcodeblock %}

### **验证规则（validation rules）**

验证规则主要用来的字段属性验证。如：

{% codeblock lang:php %}
    public function rules()
    {
        return [
            // the name, email, subject and body attributes are required
            [['name', 'email', 'subject', 'body'], 'required'],

            // the email attribute should be a valid email address
            ['email', 'email'],
        ];
    }
{% endcodeblock %}

### **批量分发（Massive Assignment）**

批量分发只应用于当前scenario下所列的*safe attributes*。如下：

{% codeblock lang:php %}
    // declared in scenario
    public function scenarios()
    {
        return [
            self::SCENARIO_LOGIN => ['username', 'password'],
            self::SCENARIO_REGISTER => ['username', 'email', 'password'],
        ];
    }

    // declared in rules
    public function rules()
    {
        return [
            [['title', 'description'], 'safe'],
        ];
    }
{% endcodeblock %}

*unsafe attributes*则无法做到批量分发，如下面加`！`的属性：

    public function scenarios()
    {
        return [
            self::SCENARIO_LOGIN => ['username', 'password', '!secret'],
        ];
    }

## **Views**
views主要通过controllers渲染并展示相关数据，这里主要讲一下`renderAjax()`如何应用，其实通过名字就可看出来，这是一个应用在ajax刷新时用的渲染方法。
基于此，首先要了解ajax的相关基础，我们知道，ajax是用来进行网页局部刷新的，`dataType`有四种形式`json,xml,script,html`,正是由于其可以传递html格式字符串，我们就可以个通过`renderAjax`渲染一个包含注册JS和CSS的局部文件（不包含layout），并返回给ajax请求，再通过ajax将代码插入到当前页面中，但面对大段ajax请求时，如此操作比单纯请求json数据，然后通过dom方式操作将返回数据写入的页面的方法更易维护。
下面通过代码讲一下实现方式（这里直通过controller和view简单叙述一下原理）：
- **controller**
{% codeblock lang:php %}
<?php
    class SiteController extends Controller
    {
        /**
        *渲染主页面
        */
        public function actionIndex()
        {
            $myName = '张三';
            $this->render('index', ['name'=>$myName]);
        }

        /**
        *ajax刷新
        */
        public function actionRenderAjax()
        {
            $newName = '李四';
            $this->renderAjax('_newName', ['newName'=>$newName]);
        }
    }
{% endcodeblock %}

- **views(包含两部分：主页面，renderAjax渲染页面)**
***主页面(index)***

{% codeblock lang:html %}
    <script>
        $('#newName').click(function(){
            $.ajax({
                type: 'POST',
                url: '/site/renderAjax',
                dataType: 'html',
                success: function (html) {
                    $('#myName').html = html;
                }
            });
        })
    </script>
    ...
    <div id="myName">
        <span>
            <?= $name ?>
        </span>
    </div>
    <input type="button" value="NewName" id="newName">
    ...
{% endcodeblock %}

***ajax页（_newName）***

{% codeblock lang:html %}
        <span>
            <?= $newName ?>
        </span>
{% endcodeblock %}

这样通过点击**NewName**按钮就可将name刷新为newName。在应对一些复杂页面的时候会比较好用。
