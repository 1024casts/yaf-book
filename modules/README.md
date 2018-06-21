# 模块(modules)

## 简介

Yaf框架是支持多模块的。如果是一个小项目，代码里既放web的代码，也放api的代码，同时也放后台的代码，那使用多模块是最好不过了。

也可以按功能来划分模块，比如用户模块，新闻模块，区块链模块等等。

多模块是可以在配置文件中开启和关闭的。使用方法也和控制器一样。

## 多模块的目录结构

目录结构如

```
// application/modules

├── modules
│   ├── Admin
│   │   ├── controllers
│   │   │   └── Index.php
│   │   └── views
│   ├── Api
│   │   └── controllers
│   │       └── Index.php
│   └── Web
│       ├── Bootstrap.php
│       ├── controllers
│       │   ├── Index.php
│       └── views
```

## 多模块的配置

通过修改配置文件，添加可以多个模块，模块名之间通过逗号分割。
其中`Index`模块是框架默认的模块, 对应的目录位置是`application/controllers`。

```
// conf/application.ini
application.modules     = Index,Web,Admin,Api
```

## 模块的定义

1. 模块的存放位置

在`application/modules`目录下，其中`modules`目录默认情况下是没有创建的，需要我们新建。

2. 模块名的命名规则

模块名要首字母大写，比如新建admin模块，在`application/modules`目录下，新建`Admin/controllers`。

创建完的目录路径为：`application/modules/Admin/controllers`

3. 模块下控制器的创建

与在`application/controllers`的创建一样， 如

```
// application/modules/Admin/controllers/Index.php
<?php

class IndexController extends Yaf\Controller_Abstract
{

    public function indexAction()
    {
        Yaf\Dispatcher::getInstance()->disableView();

        $data = [
            'title' => 'test title',
            'content' => 'test content'
        ];

        // 模板路径：application/views/index/index.phtml
        $this->getView()->display('index/index.phtml', $data)
    }
}
?>
```

## 多模块的使用

多模块的使用与默认模块的使用上多了一个模块名，区别如下

```
// 默认模块的使用，也就是请求applicatin/controllers下的控制器
http://domain.com/index/index

// 多模块的使用，比如请求后台 index控制器的index方法
http://domain.com/admin/index/index
```




