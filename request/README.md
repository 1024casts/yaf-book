# 请求

## 概述

代表了一个实际的请求，http请求或者Cli模式下的，一般不用自己去实例化，Yaf_Application(开启命名空间后为Yaf\Application)在run以后会自动根据当前请求实例它。

在浏览器请求到的 `Controller`中，可以通过`$this->getRequest()`来获取当前请求的对象。该对象为`Yaf\Request\Http` 的实例。

如果是在命令行或者终端下运行，则 `$this->getRequest()` 为 `Yaf\Request\Simple`的实例。


### Request类

#### Yaf\Request_Abstract

Request抽象类

#### Yaf\Request\Http

Web请求实体类，该类继承自`Yaf\Request_Abstract`

#### Yaf\Request\Simple

终端请求实体类，同样继承自`Yaf\Request_Abstract`, CLI模式下可以模拟一些测试，或者跑一些任务脚本。


### 获取请求信息

#### 获取服务器及环境信息

* getServer: 获取服务器变量信息，类似于$_SERVER全局变量
* getEnv: 获取环境变量信息，类似于$_ENV全局变量
* getLanguage: 获取客户端的语言信息，如zh-CN


#### 获取请求参数

* getPost: 获取POST请求数据
* getQuery: 获取查询字符串的信息
* getParam: 获取路由参数信息
* getParams:　获取所有路由参数信息
* getRequestUri: 获取请求的uri
* getMethod: 获取请求的类型
* getBaseUri: 获取请求的基本uri
* getCookie:　获取cookie信息 （可用浏览器cookie插件添加cookie来测试）
* getFiles: 获取请求中的文件信息 (可用postman测试) - Yaf-request-http里有


#### 判断请求类型

* isCli: 是否为命令行           
* isGet: 是否为get请求       
* isPost: 是否为Post请求
* isPut: 是否为PUT请求
* isHead: 是否为Head请求
* isOptions: 是否为Options请求
* isXmlHttpRequest: 是否为ajax请求


#### 与分发相关的方法

* getModuleName: 获取当前模块名称
* getControllerName: 获取当前控制器名称
* getActionName: 获取当前操作名称
* getException: 获取当前请求异常对象
* isDispatched: 是否已经分发请求
* isRouted: 是否已经将路由规则进行匹配


### 设置分发的方法

* setBaseUri: 设置基本的uri,一般不需要设置，框架会自动识别
* setModuleName: 设置模块名称
* setControllerName: 设置控制器名称
* setActionName: 设置操作名称
* setParam: 设置路由参数
* setRequestUri: 设置请求uri
* setRouted: 设置是否已经路由
* setDispatched: 设置是否已经分发路由

> 已上提到的方法都可以链式调用，如 `$this->getRequest()->setModuleName($module)->setControllerName($controller)` 。

### 完整demo

```
<?php

use Yaf\Controller_Abstract;
use Yaf\Request\Http as Request_Http;
use Yaf\Request\Simple as Request_Simple;

/**
 * Yaf\Request\Http 和 Yaf\Request\Simple 包含的方法是一样的
 * Class RequestController
 */
class RequestController extends Controller_Abstract
{
    public function init()
    {
        // 禁用模板渲染
        Yaf\Dispatcher::getInstance()->disableView();
    }

    public function serverInfoAction()
    {
        $request = $this->getRequest();

        // http://yaf-skel.com/example/request/serverInfo
        // output: Yaf_Request_Http
        echo "request class 所属实例: ";
        if ($request instanceof Request_Http) {
            echo "Yaf_Request_Http";
        } elseif ($request instanceof Request_Simple) {
            echo "Yaf_Request_Simple";
        } else {
            echo "yaf_Request_Abstract";
        }
        echo "<br/>";

        var_dump($this->getRequest()->getServer() === $_SERVER);     // true
        var_dump($this->getRequest()->getEnv() === $_ENV);           // true
        var_dump($this->getRequest()->getLanguage());                // zh-CN
    }

    /**
     * 获取请求参数
     */
    public function paramsAction()
    {
        $request = $this->getRequest();

        // http://yaf-skel.com/example/request/params?user=test&uid=1
        var_dump($request->get('user'));        // test
        var_dump($request->getQuery());         // ['user' => test, 'uid' => 1]
        var_dump($request->getQuery('user'));   // test
        var_dump($request->getPost());          // empty
        var_dump($request->getPost('user'));    // null
        var_dump($request->getParams());        // empty 
        var_dump($request->getParam('uid'));    // null
        var_dump($request->getRequestUri());    // /example/request/params
        var_dump($request->getMethod());        // GET
        var_dump($request->getBaseUri());       // ''
        var_dump($request->getCookie());        // emtpy
        var_dump($request->getFiles());         // empty
        
        // http://yaf-skel.com/example/request/params/user/test/uid/1
        var_dump($request->getParams());         // ['user' => test, 'uid' => 1] 
        var_dump($request->getParam('uid'));     // 1
        var_dump($request->getRequestUri());     // /example/request/params/user/test/uid/1

    }

    /**
     * 获取请求类型
     */
    public function methodAction()
    {
        var_dump($this->getRequest()->isCli());
        var_dump($this->getRequest()->isGet());
        var_dump($this->getRequest()->isPost());
        var_dump($this->getRequest()->isPut());
        var_dump($this->getRequest()->isXmlHttpRequest()); // 是否为ajax请求
        var_dump($this->getRequest()->isHead());
        var_dump($this->getRequest()->isOptions());
    }

    /**
     * 获取分发相关的方法
     */
    public function dispatchAction()
    {
        $this->getRequest()->setModuleName('Api');
        $this->getRequest()->setControllerName('Index');
        $this->getRequest()->setActionName('xxx');
        $this->getRequest()->setDispatched();
        $this->getRequest()->setRouted();
    }

}

```


