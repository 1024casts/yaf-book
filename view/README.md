# 视图

## 简介

Yaf框架使用原生PHP作为模板引擎, 内置的试图引擎为`Yaf_View_Simple`, 是一个简单而快速的模板引擎。

因Yaf也给用户提供了一个可扩展的的试图引擎接口`Yaf_View_Interface`，所以也可以使用自己或第三方的模板引擎来替代内置的，如Smarty、Twig(Symfony)、Blade(Laravel)等。

下面从模板的配置，使用来详细介绍下。

## 模板的配置

### 模板存放的位置

默认情况下，模板目录存放在`application/views`下

### 修改模板存放位置

有两种方式可以修改模板存放的位置

1、在启动文件中修改

在启动文件`Bootstrap.php`中新增
```
public function _initView(Yaf\Dispatcher $dispatcher)
{
    $dispatcher->initView(APP_PATH . '/templates');
}
```

2、在控制器中修改

在控制器基类中`init`方法中可以初始化试图，在基类中新增

```
public function init()
{
    $this->initView(APP_PATH . '/templates');
}
```

两者比较的话，个人更倾向于第一种。

APP_PATH的定义在
```
// public/index.php
define('APP_ROOT', dirname(__DIR__));
define('APP_PATH', APP_ROOT . '/application');
```

### 模板文件后缀

默认模板的后缀为`pthml`, 如果想修改，可以在配置文件中进行修改

```
// conf/application.ini
application.view.ext = pthml
```

### 自动寻找模板设置

默认情况下，Yaf会根据当前请求自动在指定好的模板目录里寻找，然后渲染模板并输出。
如果想关闭这个设置，有两种方法可以实现

1、全局:在启动文件中加入
```
public function _initView(Yaf\Dispatcher $dispatcher)
{
    $dispatcher->autoRender(false);
}
```

2、局部:在控制器中加入
```
public function init()
{
    Yaf\Dispatcher::getInstance()->autoRender(false);
    或
    Yaf\Dispatcher::getInstance()->disableView();
}
```

## 常用方法介绍

### setScriptPath 设置模板的目录 

`public bool Yaf_View_Simple::setScriptPath ( string $template_dir )`

示例

```
<?php
    class NewsController extends Yaf\Controller_Abstract
    {
        public function detailAction()
        {
            $this->getView()->setScriptPath(APP_PATH . '/templates/');
        }
    }
```

### getScriptPath 获取模板目录

`public string Yaf_View_Simple::getScriptPath ( void )`

示例

```
<?php
    class NewsController extends Yaf\Controller_Abstract
    {
        public function detailAction()
        {
            $viewPath = $this->getView()->getScriptPath();
        }
    }
```

### assign 为试图引擎分配一个模板变量

`public bool Yaf_View_Simple::assign ( string $name [, mixed $value ] )`

$name 参数可以为字符串或数组， 如果为字符串，则$value不能为空。 为数组其实就是批量分配变量。

示例

```
<?php
    class NewsController extends Yaf\Controller_Abstract
    {
        public function detailAction()
        {
            //字符串
            $this->getView()->assign('title', 'test title');

            //数组
            $data = [
                'title'=>'test tile', 
                'content'=>'this is a test for phpcasts'
            ];
            $this->getView()->assign($data);
        }
    }
```

### display 渲染一个试图模板，并直接输出给请求端

`public bool Yaf_View_Simple::display ( string $tpl [, array $tpl_vars ] )`

在使用`display`前，需要关闭模板的自动定位，否则会执行两次`display`.

> 关闭模板的自动定位可以参看：自动寻找模板设置。

示例

```
<?php
    class NewsController extends Yaf\Controller_Abstract
    {

        public function detailAction()
        {
            \Yaf\Dispatcher::getInstance()->autoRender(false);

            $this->getView()->assign('title', 'this is a test')->display('index/index.phtml');

            // 或者一个方法搞定，渲染+分配变量
            $this->getView()->display('index/index.phtml', ['title' => 'this is a test']);
        }
    }
```

### render 渲染模板

`public string Yaf_View_Simple::render ( string $tpl [, array $tpl_vars ] )`

该方法只返回渲染后的内容，并不会输出任何内容， 可以通过下面代码对比理解

```
public function render($name, $value = NULL) 
{
    return $this->getViewEngine()->fetch($name);
}

public function display($name, $value = NULL) 
{
    echo $this->getViewEngine()->fetch($name);
}
```

对比发现，render其实是return了模板内容，display是echo了模板内容。

### clear 清除模板变量

`public bool Yaf_View_Simple::clear ([ string $name ] )`

清楚指定的变量, 参数如果为空，将会清除所有的变量。

示例

```
<?php
class NewsController extends Yaf_Controller_Abstract 
{
    public function detailAction() 
    {

        $this->getView()->assign('title', 'this is a test');

        $data = [
            'content' => 'this is a test content',
            'created_at' => '2018-03-13'
        ];
        $this->getView()->assign($data);

        // clear "title" and "content"
        $this->getView()->clear("title")->clear("content"); 

        //清楚所有变量
        $this->_view->clear(); 
    }
?>
```

## 集成第三方试图引擎

### Smarty

### Twig

### Blade


