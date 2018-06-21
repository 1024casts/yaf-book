# 错误和异常

### 简介

Yaf实现了一套错误和异常捕获机制, 主要是对常见的错误处理和异常捕获方法做了一个简单抽象, 方便应用组织自己的错误统一处理逻辑.

Yaf自身出错时候, 根据配置可以分别采用抛异常或者触发错误的方式来通知错误. 在appliation.dispatcher.throwException(配置文件, 或者通过Yaf_Dispatcher::throwException(true))打开的情况下, Yaf会抛异常, 否则则会触发错误.

### 异常分类

| 错误类型 | 错误说明 | 
| :- | :- | 
| Yaf\Exception_TypeError | 类型错误 |
| Yaf\Exception_StartupError | 启动失败 |
| Yaf\Exception_DispatchFailed | 路由分发失败 |
| Yaf\Exception_RouterFailed | 路由失败|
| Yaf\Exception_LoadFailed | 文件加载失败(文件不存在)|
| Yaf\Exception_LoadFailed_Module | 加载模块失败(模块不存在)|
| Yaf\Exception_LoadFailed_Controller | 加载控制器失败(控制器类不存在)|
| Yaf\Exception_LoadFailed_Action | 加载操作失败(方法不存在)|
| Yaf\Exception_LoadFailed_View | 加载模板文件失败(一般是路径不对或后缀问题)|


### 错误常量

| 常量 | 常量值 | 备注 | 
| :- | :- | :- | 
|YAF\ERR\STARTUP_FAILED	|512|	启动失败|
|YAF\ERR\ROUTE_FAILED	|513|	路由失败|
|YAF\ERR\DISPATCH_FAILED	|514|	路由分发失败|
|YAF\ERR\AUTOLOAD_FAILED	|520|	自动加载失败|
|YAF\ERR\NOTFOUND\MODULE	|515|	加载模块失败|
|YAF\ERR\NOTFOUND\CONTROLLER	|516|	加载控制器失败|
|YAF\ERR\NOTFOUND\ACTION	|517|	加载方法失败|
|YAF\ERR\NOTFOUND\VIEW	|518|	加载模板失败|
|YAF\ERR\CALL_FAILED	|519|	调用方法失败|
|YAF\ERR\TYPE_ERROR	|521|	类型错误|

### 异常的捕获与处理

若果想在Yaf中抛出异常，可以通过配置文件来开启

```
// conf/application.ini, 该值不配置默认也是开启的
application.dispatcher.throwException = true
```

#### 配置异常捕获

有两种方式来开启捕获异常

 1、 通过配置文件

```
// conf/application.ini
application.dispatcher.catchException = true
```

 2、 在启动文件中开启

```
// application/Bootstrap.php
/**
 * 初始化路由
 */
public function _initRoute()
{
    \Yaf\Dispatcher::getInstance()->catchException(true);
}
```

#### 处理异常

也有两种方式来处理

 1、 在`ErrorController`控制器中处理

```
<?php

/**
 * 当有未捕获的异常, 则控制流会流到这里
 */
class ErrorController extends Yaf\Controller_Abstract
{
    /**
     * 也可通过 $this->getRequest()->getException() 获取到发生的异常
     */
    public function errorAction($exception)
    {
        switch($exception->getCode()) {
            case YAF_ERR_LOADFAILD:
            case YAF_ERR_LOADFAILD_MODULE:
            case YAF_ERR_LOADFAILD_CONTROLLER:
            case YAF_ERR_LOADFAILD_ACTION:
                //404
                header("Not Found");

                break;
            case CUSTOM_ERROR_CODE:
                //自定义的异常
                ....
                break;
        }

        // 如果想将错误码和错误消息输出到模板文件，可以这样
        $this->getView()->assign("code", $exception->getCode());
        $this->getView()->assign("message", $exception->getMessage());
    }
}
?>
```

 2、 使用Dispatcher的`setErrorHandler`方法捕获

使用前需要关闭抛出异常，即`throwException(false)`

 ```
function appErrorHandler($errno, $errmsg, $errFile, $errLine, $app)
{
    //这里可以写日志，输出错误消息到模板等等
}
 ```

 示例

 ```
 // public/index.php
 <?php

define('APP_ROOT', dirname(__DIR__));
define('APP_PATH', APP_ROOT . '/application');

// 该函数可以定义到初始化的文件中，然后通过\Yaf\Loader::import() 导入进来
function appErrorHandler($errno, $errmsg, $errFile, $errLine, $app)
{
    // 具体的错误处理

    //也可以通过func_get_args()方法来查看传入的参数
    //var_dump(func_get_args());
}

$application = new Yaf\Application(APP_ROOT . "/conf/application.ini", \Yaf\ENVIRON);
$application->getDispatcher()->throwException(FALSE)->setErrorHandler('appErrorHandler', E_ALL ^E_NOTICE)
->getApplication()->bootstrap()->run();
 ```