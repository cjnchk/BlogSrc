layout: post
title: "yii2过滤器：Filters"
date: 2015-08-27 20:59:58
categories:
tags:
keywords:
description:
comments: true
photos:
-
---
过滤器是一种运行于控制器动作之前或之后的对象。例如，运行于动作之前，用于确认终端用户是否有权访问的访问控制过滤器；运行与动作之后，用于压缩输出的内容压缩过滤器。

过滤器通常包含前置过滤器和后置过滤器。

## **过滤器的使用**

过滤器实际是一类特殊的行为（behaviors）。因此，过滤器的使用跟行为的使用是一样的。我们可以通过重写控制器类中`behaviors()`方法来声明过滤器，如下：
```
    public function behaviors()
    {
        return [
            [
                'class' => 'yii\filters\HttpCache',
                'only' => ['index', 'view'],
                'lastModified' => function ($action, $params) {
                    $q = new \yii\db\Query();
                    return $q->from('user')->max('updated_at');
                },
            ],
        ];
    }
```
默认情况下，在控制器类中声明的过滤器会应用到当前控制器下的所有动作。But，我们可以通过配置`only`属性来指定当前过滤器应用的动作，如上例。我们也可以通过配置`except`属性来指出哪些动作不应用于当前过滤器。
<!--more-->
除在控制器内以外，我们还可以在应用或模块内声明过滤器。如此以来过滤器将应用于当前应用或模块的所有控制器动作，除非在`only`或`except`中配置。

##### *注意：在模块或应用中声明过滤器，配置`only`或`except`属性时应当使用路由而非动作ID。因为单纯的一个动作ID无明确当前应用或模块范围内的动作。*

当多个过滤器共同作用于一个动作时，他们会按照如下规则顺序执行：
- 前置过滤器
 + 应用中的过滤器，按照`behaviors()`中列表正序依次作用
 + 模块中的过滤器，按照`behaviors()`中列表正序依次作用
 + 控制器中的过滤器，按照`behaviors()`中列表正序依次作用
 + 如果这些过滤其中的任何一个取消了动作的执行，那么之后的所有过滤器都将不再起作用，包括前置和后置过滤器。
- 如果动作通过了所有过滤，那么动作将会执行。
- 后置过滤器
 + 控制器中的过滤器，按照`behaviors()`中列表逆序依次作用
 + 模块中的过滤器，按照`behaviors()`中列表逆序依次作用
 + 应用中的过滤器，按照`behaviors()`中列表逆序依次作用

## **创建过滤器**
为了创建动作过滤器，需要继承`yii\base\ActionFilter`并且重写`beforeAction()`和`afterAction()`两个方法，这两个方法分别作用于动作执行前和执行后。`beforeAction()`的返回值决定了动作是否执行，如果其返回`false`，动作将不会执行，后之过滤器也不会起作用。

下面是一个记录动作执行时间的过滤器：
```
    namespace app\components;

    use Yii;
    use yii\base\ActionFilter;

    class ActionTimeFilter extends ActionFilter
    {
        private $_startTime;

        public function beforeAction($action)
        {
            $this->_startTime = microtime(true);
            return parent::beforeAction($action);
        }

        public function afterAction($action, $result)
        {
            $time = microtime(true) - $this->_startTime;
            Yii::trace("Action '{$action->uniqueId}' spent $time second.");
            return parent::afterAction($action, $result);
        }
    }
```

## ** 核心过滤器 **

Yii提供了许多常见的过滤器，主要构建在`yii\filters`命名空间下，下面将做一些简单介绍。

### ** 访问控制 **

访问控制过滤器提供基于规则的简单访问控制功能。通常，动作执行前，访问控制器会检查规则，并且找到一个能够匹配当前内容的变量，次规则将将决定动作是否执行，如果没有规则匹配到，访问将被阻止。

下面的例子展示了授权用户访问`create`、`update`动作，非授权用户则无访问。
```
    use yii\filters\AccessControl;

    public function behaviors()
    {
        return [
            'access' => [
                'class' => AccessControl::className(),
                'only' => ['create', 'update'],
                'rules' => [
                    // allow authenticated users
                    [
                        'allow' => true,
                        'roles' => ['@'],
                    ],
                    // everything else is denied by default
                ],
            ],
        ];
    }
```
关于访问控制的跟多细节参考[身份验证](http://www.yiiframework.com/doc-2.0/guide-security-authorization.html)章节。

#### **身份验证过滤器**

身份验证过滤器使用多种方式来验证用户，例如`HTTP Basic Auth`，`OAuth 2`。所有这些过滤类都位于`yii\filters\auth`命名空间下。
下面的例子介绍了如何通过`yii\filters\auth\HttpBasicAuth`利用用户token通过HTTP Basic Auth方法验证用户是否有操作权限。 要注意的是，为了是上面的方法正常工作，需要在用户验证类（[user identity class ](http://www.yiiframework.com/doc-2.0/yii-web-user.html#$identityClass-detail)）里实现`findIdentityByAccessToken()`方法。
```
    use yii\filters\auth\HttpBasicAuth;

    public function behaviors()
    {
        return [
            'basicAuth' => [
                'class' => HttpBasicAuth::className(),
            ],
        ];
    }
```
身份验证过滤器通常用作实现RESTful([表现层状态转化](http://www.ruanyifeng.com/blog/2011/09/restful)) APIs，具体参考RESTful [Authentication](http://www.yiiframework.com/doc-2.0/guide-rest-authentication.html)章节。

### **内容判定（ContentNegotiator）**
内容判定支持返回数据格式判定，应用语言判定。他会通过检查`GET`参和`HTTP`决定返回什么样的数据格式和语言。
下面的例子，内容判定器支持格式化`json`和`xml`类型数据，English和German语言。
```
    use yii\filters\ContentNegotiator;
    use yii\web\Response;

    public function behaviors()
    {
        return [
            [
                'class' => ContentNegotiator::className(),
                'formats' => [
                    'application/json' => Response::FORMAT_JSON,
                    'application/xml' => Response::FORMAT_XML,
                ],
                'languages' => [
                    'en-US',
                    'de',
                ],
            ],
        ];
    }
```
在应用生命周期内，返回数据格式和语言应当尽早确定。因此，内容判定器更应该作为启动引导组件，而不仅仅是一个过滤器。下面是一个在应用配置中配置的例子。
```
    use yii\filters\ContentNegotiator;
    use yii\web\Response;

    [
        'bootstrap' => [
            [
                'class' => ContentNegotiator::className(),
                'formats' => [
                    'application/json' => Response::FORMAT_JSON,
                    'application/xml' => Response::FORMAT_XML,
                ],
                'languages' => [
                    'en-US',
                    'de',
                ],
            ],
        ],
    ];
```
*注意：当应用无法通过请求判定内容类型和语言时，列表中排在首位的内容类型和语言格式将被使用。*

### **HttpCache**

HttpCache利用HTTP头的`Last-Modified`、`Etag`是客户端的缓存，例如：
```
    use yii\filters\HttpCache;

    public function behaviors()
    {
        return [
            [
                'class' => HttpCache::className(),
                'only' => ['index'],
                'lastModified' => function ($action, $params) {
                    $q = new \yii\db\Query();
                    return $q->from('user')->max('updated_at');
                },
            ],
        ];
    }
```
具体应用细节参考[HTTP Caching](http://www.yiiframework.com/doc-2.0/guide-caching-http.html)章节。

### **PageCache**
PageCache实现服务器端的整页缓存。PageCache被用于`index`动作上，以实现全页面的最大60s缓存，或者改变，也可根据应用的语言来存储多个对应版本。
```
    use yii\filters\PageCache;
    use yii\caching\DbDependency;
    public function behaviors()
    {
        return [
            'pageCache' => [
                'class' => PageCache::className(),
                'only' => ['index'],
                'duration' => 60,
                'dependency' => [
                    'class' => DbDependency::className(),
                    'sql' => 'SELECT COUNT(*) FROM post',
                ],
                'variations' => [
                    \Yii::$app->language,
                ]
            ],
        ];
    }
```
具体应用细节参考[Page Caching](http://www.yiiframework.com/doc-2.0/guide-caching-page.html)章节。

### **限速（RateLimiter）**
RateLimiter基于[漏桶算法](http://en.wikipedia.org/wiki/Leaky_bucket)（leaky bucklet algorithm）实现了一个限速算法。主要用来实现RESTful APIs，具体应用细节参考[Rate Limiter](http://www.yiiframework.com/doc-2.0/guide-rest-rate-limiting.html)章节。

### **数据提交动作过滤（VerbFilter）**
VerbFilter检查HTTP提交动作是否允许应到具体action。如果不允许，将会抛出HTTP 405异常。例如，通过VerbFIlter声明CRUD操作允许的请求方式：
```
    use yii\filters\VerbFilter;

    public function behaviors()
    {
        return [
            'verbs' => [
                'class' => VerbFilter::className(),
                'actions' => [
                    'index'  => ['get'],
                    'view'   => ['get'],
                    'create' => ['get', 'post'],
                    'update' => ['get', 'put', 'post'],
                    'delete' => ['post', 'delete'],
                ],
            ],
        ];
    }
```

### **跨域资源共享（CORS：Cross-Origin Resource Sharing ）**
跨域资源共享是一个允许外站访问本站资源的资源共享机制。比如，JS的Ajax调用就可以利用XMLHttpRquest机制。基于同源策略，类似这样的跨站请求会被WEB浏览器禁止。CORS定义了一种浏览器和服务器可以直接决定是否允许跨站请求的方式。
Cores Filter应该定义在Authentication/Authorization Filter之前，以便CORS头能够发送。
```
    use yii\filters\Cors;
    use yii\helpers\ArrayHelper;

    public function behaviors()
    {
        return ArrayHelper::merge([
            [
                'class' => Cors::className(),
            ],
        ], parent::behaviors());
    }
```
Cors Filter可以通过cors属性作调整：
- `cors['Origin']`：此数组定义允许访问的源。可以是`['*']`(所有人)或者`['http://www.myserver.net', 'http://www.myotherserver.com']`。默认`['*']`
- `cors['Access-Control-Request-Method']`：此数组定义允许访问的动作，例如`['GET', 'OPTIONS', 'HEAD']`，默认`['GET', 'POST', 'PUT', 'PATCH', 'DELETE', 'HEAD', 'OPTIONS']`
- `cors['Access-Control-Request-Headers']`：此数组定义允许访问的头。可以是`['*']`，代表所有头，或者指定为`['X-Request-With']`。默认`['*']`
- `cors['Access-Control-Allow-Credentials']`：定义当前请求是否需要被验证。可以是`true`、`false`或`null`（未设置）。默认`null`
- `cors['Access-Control-Max-Age']`： pre-flight请求生命周期。默认`86400`

例如，CORS允许站点：`http://www.myserver.net`，通过`GET`、`HEAD`、`OPTIONS`方式：
```
    use yii\filters\Cors;
    use yii\helpers\ArrayHelper;

    public function behaviors()
    {
        return ArrayHelper::merge([
            [
                'class' => Cors::className(),
                'cors' => [
                    'Origin' => ['http://www.myserver.net'],
                    'Access-Control-Request-Method' => ['GET', 'HEAD', 'OPTIONS'],
                ],
            ],
        ], parent::behaviors());
    }
```
你可以在某一个action的基础上通过覆盖默认参数来调整CORS头，例如，可通过如下方式为`login`动作添加`Access-Control-Allow-Credentials`：
```
    use yii\filters\Cors;
    use yii\helpers\ArrayHelper;

    public function behaviors()
    {
        return ArrayHelper::merge([
            [
                'class' => Cors::className(),
                'cors' => [
                    'Origin' => ['http://www.myserver.net'],
                    'Access-Control-Request-Method' => ['GET', 'HEAD', 'OPTIONS'],
                ],
                'actions' => [
                    'login' => [
                        'Access-Control-Allow-Credentials' => true,
                    ]
                ]
            ],
        ], parent::behaviors());
    }
```
