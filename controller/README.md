# 控制器

## 简介

`Yaf_Controller_Abstract` 是Yaf的MVC体系的核心部分。 MVC是指Model-View-Controller, 是一个用于分离应用逻辑和表现逻辑的设计模式。

每个用户自定义controller都应当继承`Yaf_Controller_Abstract`。

你会发现在你自己的controller中无法定义`__construct`方法。因此，Yaf_Controller_Abstract 提供了一个魔术方法Yaf_Controller_Abstract::init()。

如果在你自己的controller里面已经定义了一个init()方法，当你的controller被实例化的时候，它将被调用。

Action可能需要参数，当一个请求来到的时候，在路由中如果请求的参数有相同名称的变量（例如：Yaf_Request_Abstract::getParam()）， Yaf将把他们传递给action方法（see Yaf_Action_Abstract::execute()）

> 已上引用自PHP官方文档

## 控制器的定义

### 控制器文件

    - 文件位置:  位于application/controllers 或 application/modules/controllers 目录下
    - 文件命名:  首字母大写，不含Controller, 例如：User.php

### 控制器类

    - 类定义: 控制器明文件名+Controller, eg.: UserController
    - 类继承: 一般需要继承基类Yaf\Controller_Abstract或Yaf\Controller_Abstract的子类

### 控制器方法

    - 方法定义: 方法名+Action (对外可以访问)
    - 初始化方法: 代替自带的__construct, 在yaf中init都会被默认执行
    - 获取参数的方法:
        - 通过方法参数获取， 如`public indexAction($name, $id)`
        - 通过request对象获取

        ```
        public indexAction($name, $id) {
           assert($name == $this->getRequest()->getParam("name"));
           assert($id   == $this->_request->getParam("id"));
        }
        ```

### 命名空间

    Yaf框架的控制器默认情况下不需要指定namespace, 即使在多模块的情况下也不需要区分。这一点需要注意下。


### 示例

    ```
    // application/controllers/User.php
    // or
    // application/modules/Web/controllers/User.php

    <?php

    // 这里无需使用namespace

    class UserController extends \Yaf\Controller_Abstract
    {
        // 该方法默认会被执行
        public function init()
        {

        }

        public function messageAction()
        {

        }
    }
    ?>

    ```


## 独立文件Action的定义

    - 作用: 为了分解大的控制器， 让代码更清晰
    - 与控制器绑定，即将操作绑定到某个类上，如

        ```
        <?php
         class TestController extends \Yaf\Controller_Abstract
         {
             public $actions = array(
                 'index'=>'actions/index.php',
                 'add'=>'actions/add.php',
                 'detail'=>'actions/detail.php'
             );
         }
        ```

    - 定义action类文件

        - 位置: 一般在application/actions下面，如detail.php(application/actions/detail.php)
        - 命名: 必须以Action为后缀，如DetailAction
        - 继承: 继承自Yaf\Action_abstract
        - 必须实现的方法: execute,因为其父类Yaf\Action_abstract是一个抽象类，必须实现抽象方法execute
        - 可以使用父类的方法: 不仅可以使用Yaf\Action_abstract的方法，还可以使用Yaf\Controller_Abstract的方法(因为Yaf\Action_abstract继承自Yaf\Controller_Abstract)

    - execute方法中获取参数

        - 通过方法参数获取， 如`public execute($name, $id)`
        - 通过request对象获取

        ```
        /**
         * Now assuming we are using the Yaf_Route_Static route
         * for request: http://yourdomain/product/list/name/yaf/id/22
         * will result:
         * bool(true)
         * bool(true)
         */
        public indexAction($name, $id) {
           assert($name == $this->getRequest()->getParam("name"));
           assert($id   == $this->getRequest()->getParam("id"));
        }
        ```


## 控制器类方法

主要说明下常用类方法的使用

```
abstract Yaf\Controller_Abstract
{
    /* 属性 */
    public $actions ;
    protected $_module ;
    protected $_name ;
    protected $_request ;
    protected $_response ;
    protected $_invoke_args ;
    protected $_view ;

    /* 方法 */
    final private void __clone ( void )
    final private __construct ( void )
    protected bool display ( string $tpl [, array $parameters ] )
    public void forward ( string $module [, string $controller [, string $action [, array $paramters ]]] )
    public void getInvokeArg ( string $name )
    public void getInvokeArgs ( void )
    public string getModuleName ( void )
    public Yaf_Request_Abstract getRequest ( void )
    public Yaf_Response_Abstract getResponse ( void )
    public Yaf_View_Interface getView ( void )
    public void getViewpath ( void )
    public void init ( void )
    public void initView ([ array $options ] )
    public void redirect ( string $url )
    protected string render ( string $tpl [, array $parameters ] )
    public void setViewpath ( string $view_directory )
    }
```

### 初始化操作

     - init: 初始化操作(如初始化实例变量，替代__construct的功能)
     - initView: 初始化试图

### 试图操作

    - display: 显示数据
    - getView: 获取当前的试图引擎
    - getViewPath: 获取试图模板路径
    - initView: 初始化试图
    - render: 渲染试图模板
    - setViewPath: 指定试图模板路径

### 请求与响应

     - getRequest: 获取当前请求对象，那么它就可以调用request对象所有的方法
     - getResponse: 获取当前响应对象，那么它就可以调用response对象所有的方法

### 页面跳转

 - forward: 将当前的请求转交给另外的Action，forward后不会直接立即跳转到目的Action执行而是会在当前的Action执行完成后，下一轮的DispatchLoop中，交给目的Action. 所以, 如果你希望立即跳转到目的Action, 那么请使用 `return false` 结束当前的执行流程。 支持跳转到其他模块的某个控制器的某个方法。

支持的格式
```
public boolean Yaf_Controller_Abstract::forward( string  $action ,
                                                 array  $params = NULL );

public boolean Yaf_Controller_Abstract::forward( string  $controller ,
                                                 string  $action ,
                                                 array  $params = NULL );

public boolean Yaf_Controller_Abstract::forward( string  $module ,
                                                 string  $controller ,
                                                 string  $action ,
                                                 array  $params = NULL );
```

参数说明:
$module 要转给动作的模块, 注意要首字母大写, 如果为空, 则转给当前模块
$controller 要转给动作的控制器, 注意要首字母大写, 如果为空, 则转给当前控制器
$action 要转给的动作, 注意要全部小写
$params 关联数组, 附加的参数, 可通过Yaf_Request_Abstract::getParam获取

 - redirect: 重定向到一个指定的URL，无需手动结束当前流程, 内部实现是通过发送Location报头实现，如：header("Location:https://www.phpcasts.org/")

两个函数功能十分类似，区别在于redirect不能附带数据。而forward的第四个参数是一个数组，可以转发键值对数据。可在目的controller中由$this->getRequest()->getParam()获取。

 例如：登录权限控制

 - forward 跳转到当前控制器的
 ```
 <?php
    class IndexController extends \Yaf\Controller_Abstract
    {
        public function indexAction()
        {
             $logined = $_SESSION["userId"];
             if (!$logined) {
                 $this->forward("login", array("from" => "Index")); // 跳转到当前模块的login操作

                 return FALSE;  //立即返回就不会执行后面的内容了
             }

             //显示内容
        }

        public function loginAction() {
             echo "login, redirected from ", $this->_request->getParam("from") , " action";
        }
    }
 ?>
 ```

 - forward 跳转到其他模块

 ```
 //跳转到admin模块的login控制器的index方法
 $this->forward('admin', 'login', 'index',array('from'=>'index'));
 ```

 - redirect 到某个url
 ```
 public function indexAction()
 {
    $logined = $_SESSION['user_id'];
    if (!$logined) {
        $this->redirect('http://www.domain.com/admin/login/index')); // 跳转到http://www.domain.com/admin/login/index这个地址

        return FALSE;  //立即返回就不会执行后面的内容了
    }

 }
 ```

## 错误处理控制器

Yaf中可以通过ErrorController(application/controllers/Error.php)控制器来捕获异常与错误。

捕获异常与错误需要开启相应的配置，有两种方式:

 - 通过配置文件开启

 ```
 // conf/application.ini
 application.dispatcher.catchException = true
 ```

 - 在框架启动文件中开启

 ```
 // application/Bootstrap.php
 public function _initException(Yaf\Dispatcher $dispatcher)
 {
     $dispatcher::getInstance()->catchException(true);
 }

 ```

 - 示例

 ```
    // application/controllers/Error.php
    <?php
    class ErrorController extends Yaf\Controller_Abstract
    {
        /**
         * @param Exception $exception
         */
         public function errorAction(Exception $exception)
         {
            $constArr = array(
                YAF\ERR\NOTFOUND\MODULE,
                YAF\ERR\NOTFOUND\CONTROLLER,
                YAF\ERR\DISPATCH_FAILED,
                YAF\ERR\NOTFOUND\ACTION,
                YAF\ERR\NOTFOUND\VIEW
            );
            $err = $exception->getCode();
            if (in_array($err, $constArr)) {
                $code = 404;
                $message = '请求的页面不存在';
            } else {
                $code = 500;
                $message = '系统出错';
            }
            if (ENV == 'DEV') {
                $message = $exception->getMessage();
            }
            //记录日志
            //ajax输出或显示错误模板
            $this->getView()->assign('message', $message);
        }
    }
 ```

 > 关于异常处理后面会有详细的介绍# 控制器

## 简介

`Yaf_Controller_Abstract` 是Yaf的MVC体系的核心部分。 MVC是指Model-View-Controller, 是一个用于分离应用逻辑和表现逻辑的设计模式。

每个用户自定义controller都应当继承`Yaf_Controller_Abstract`。

你会发现在你自己的controller中无法定义`__construct`方法。因此，Yaf_Controller_Abstract 提供了一个魔术方法Yaf_Controller_Abstract::init()。

如果在你自己的controller里面已经定义了一个init()方法，当你的controller被实例化的时候，它将被调用。

Action可能需要参数，当一个请求来到的时候，在路由中如果请求的参数有相同名称的变量（例如：Yaf_Request_Abstract::getParam()）， Yaf将把他们传递给action方法（see Yaf_Action_Abstract::execute()）

> 已上引用自PHP官方文档

## 控制器的定义

### 控制器文件

 - 文件位置:  位于application/controllers 或 application/modules/controllers 目录下
 - 文件命名:  首字母大写，不含Controller, 例如：User.php

### 控制器类

 - 类定义: 控制器明文件名+Controller, eg.: UserController
 - 类继承: 一般需要继承基类Yaf\Controller_Abstract或Yaf\Controller_Abstract的子类

### 控制器方法

 - 方法定义: 方法名+Action (对外可以访问)
 - 初始化方法: 代替自带的__construct, 在yaf中init都会被默认执行
 - 获取参数的方法:

  - 通过方法参数获取， 如`public indexAction($name, $id)`
  - 通过request对象获取

        ```
        public indexAction($name, $id) {
           assert($name == $this->getRequest()->getParam("name"));
           assert($id   == $this->_request->getParam("id"));
        }
        ```

### 命名空间

    Yaf框架的控制器默认情况下不需要指定namespace, 即使在多模块的情况下也不需要区分。这一点需要注意下。


### 示例

```
// application/controllers/User.php
// or
// application/modules/Web/controllers/User.php

<?php

// 这里无需使用namespace

class UserController extends \Yaf\Controller_Abstract
{
    // 该方法默认会被执行
    public function init()
    {

    }

    public function messageAction()
    {

    }
}
?>

```


## 独立文件Action的定义

 - 作用: 为了分解大的控制器， 让代码更清晰
 - 与控制器绑定，即将操作绑定到某个类上，如

        ```
        <?php
         class TestController extends \Yaf\Controller_Abstract
         {
             public $actions = array(
                 'index'=>'actions/index.php',
                 'add'=>'actions/add.php',
                 'detail'=>'actions/detail.php'
             );
         }
        ```

 - 定义action类文件

  - 位置: 一般在application/actions下面，如detail.php(application/actions/detail.php)
  - 命名: 必须以Action为后缀，如DetailAction
  - 继承: 继承自Yaf\Action_abstract
  - 必须实现的方法: execute,因为其父类Yaf\Action_abstract是一个抽象类，必须实现抽象方法execute
  - 可以使用父类的方法: 不仅可以使用Yaf\Action_abstract的方法，还可以使用Yaf\Controller_Abstract的方法(因为Yaf\Action_abstract继承自Yaf\Controller_Abstract)

  - execute方法中获取参数

    - 通过方法参数获取， 如`public execute($name, $id)`
    - 通过request对象获取

        ```
        /**
         * Now assuming we are using the Yaf_Route_Static route
         * for request: http://yourdomain/product/list/name/yaf/id/22
         * will result:
         * bool(true)
         * bool(true)
         */
        public indexAction($name, $id) {
           assert($name == $this->getRequest()->getParam("name"));
           assert($id   == $this->getRequest()->getParam("id"));
        }
        ```


## 控制器类方法

主要说明下常用类方法的使用

```
abstract Yaf\Controller_Abstract
{
    /* 属性 */
    public $actions ;
    protected $_module ;
    protected $_name ;
    protected $_request ;
    protected $_response ;
    protected $_invoke_args ;
    protected $_view ;

    /* 方法 */
    final private void __clone ( void )
    final private __construct ( void )
    protected bool display ( string $tpl [, array $parameters ] )
    public void forward ( string $module [, string $controller [, string $action [, array $paramters ]]] )
    public void getInvokeArg ( string $name )
    public void getInvokeArgs ( void )
    public string getModuleName ( void )
    public Yaf_Request_Abstract getRequest ( void )
    public Yaf_Response_Abstract getResponse ( void )
    public Yaf_View_Interface getView ( void )
    public void getViewpath ( void )
    public void init ( void )
    public void initView ([ array $options ] )
    public void redirect ( string $url )
    protected string render ( string $tpl [, array $parameters ] )
    public void setViewpath ( string $view_directory )
    }
```

### 初始化操作

 - init: 初始化操作(如初始化实例变量，替代__construct的功能)

### 试图操作

 - initView: 初始化试图
 - display: 显示数据
 - getView: 获取当前的试图引擎
 - getViewPath: 获取试图模板路径
 - initView: 初始化试图
 - render: 渲染试图模板
 - setViewPath: 指定试图模板路径

### 请求与响应

 - getRequest: 获取当前请求对象，那么它就可以调用request对象所有的方法
 - getResponse: 获取当前响应对象，那么它就可以调用response对象所有的方法

### 页面跳转

 - fowrard:

 将当前的请求转交给另外的Action（对用户来说是透明的，相当于Web服务器的代理）.
 调用Yaf\Controller_Abstract::forward()以后，不会直接立即跳转到目的Action执行，
 而是会在当前的Action执行完成后，下一轮的DispatchLoop中，交给目的Action.
 所以, 如果你希望立即跳转到目的Action, 那么请使用return结束当前的执行流程.

  - 跳转到某个控制器的某个方法，不会立即跳转，会继续执行他后面的内容
  - 支持跳转到其他模块的某个控制器的某个方法

 - redirect: 跳转到一个指定的url


 例如：登录权限控制

 - forward 跳转到当前控制器的

 ```
 <?php
    class IndexController extends \Yaf\Controller_Abstract
    {
        public function indexAction()
        {
             $logined = $_SESSION["userId"];
             if (!$logined) {
                 $this->forward("login", array("from" => "Index")); // 跳转到当前模块的login操作

                 return FALSE;  //立即返回就不会执行后面的内容了
             }

             //显示内容
        }

        public function loginAction() {
             echo "login, redirected from ", $this->_request->getParam("from") , " action";
        }
    }
 ?>
 ```

 - forward 跳转到其他模块

 ```
 //跳转到admin模块的login控制器的index方法
 $this->forward('admin', 'login', 'index',array('from'=>'index'));
 ```

 - redirect

 将当前请求重定向到指定的URL（内部实现是通过发送Location报头实现，如：header("Location:http//www.phpcasts.org/"）

 ```
 public function indexAction()
 {
    $logined = $_SESSION['user_id'];
    if (!$logined) {
        $this->redirect('http://www.domain.com/admin/login/index')); // 跳转到http://www.domain.com/admin/login/index这个地址

        return FALSE;  //立即返回就不会执行后面的内容了
    }

 }
 ```

## 错误处理控制器

Yaf中可以通过ErrorController(application/controllers/Error.php)控制器来捕获异常与错误。

捕获异常与错误需要开启相应的配置，有两种方式:

 - 通过配置文件开启

 ```
 // conf/application.ini
 application.dispatcher.catchException = true
 ```

 - 在框架启动文件中开启

 ```
 // application/Bootstrap.php
 public function _initException(Yaf\Dispatcher $dispatcher)
 {
     $dispatcher::getInstance()->catchException(true);
 }

 ```

 - 示例

 ```
    // application/controllers/Error.php
    <?php
    class ErrorController extends Yaf\Controller_Abstract
    {
        /**
         * @param Exception $exception
         */
         public function errorAction(Exception $exception)
         {
            $constArr = array(
                YAF\ERR\NOTFOUND\MODULE,
                YAF\ERR\NOTFOUND\CONTROLLER,
                YAF\ERR\DISPATCH_FAILED,
                YAF\ERR\NOTFOUND\ACTION,
                YAF\ERR\NOTFOUND\VIEW
            );
            $err = $exception->getCode();
            if (in_array($err, $constArr)) {
                $code = 404;
                $message = '请求的页面不存在';
            } else {
                $code = 500;
                $message = '系统出错';
            }
            if (ENV == 'DEV') {
                $message = $exception->getMessage();
            }
            //记录日志
            //ajax输出或显示错误模板
            $this->getView()->assign('message', $message);
        }
    }
 ```

 > 关于异常处理后面会有详细的介绍
