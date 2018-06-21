# 模型

## 概述

> Yaf框架中本身是没有提供数据库操作相关的类，但是我们可以集成第三方的ORM(比如Eloquent, Doctrine), 或者对PDO简单封装的类库。

## 模型定义

 - 文件位置： 位于`application/models`目录下
 - 命名规则： 首字母大写的形式，不含 Model，如`User.php`
 - 类定义： 文件名+Model 作为类名，如 `class UserModel extend Model {}`
 - 继承： 继承自Model基类（ORM或者基于PDO封装好的基类）

 示例:

 ```php
 <?php

 use PHPCasts\Model

 class UserModel extends Model
 {

 }

 ```

## ORM

对象关系映射 (Object Relational Mapping)

### 基本功能

 - 连接数据库
 - CURD功能

### 实现

可以选用自己熟悉的一个第三方的ORM，或者封装好的类库，常见的有如下几种：

 - Eloquent（推荐）
 - Doctrine
 - Medoo
 - 自己封装的类库 Model


> 其中 `Eloquent`, `Doctrine` 内部封装的比较抽象，功能也挺强大，但是性能差一点，适用于OA、CMS等系统使用
> `Medoo` 和 `自己封装的类库` 因为是对PDO的简单封装，所以性能相对对好很多。 适用于Web或者API等性能要求高的场景。

 以`Eloquent` 为例：

 - 连接数据库
 - CURD操作
 - 调试

## 使用

### Eloquent

Laravel的自带的ORM，可以很方便的与其他框架配合使用。


#### 安装Eloquent

```
composer require illuminate/database -vvv
```

### 配置数据库

```
// conf/application.ini
; database for Eloquent
database.driver = mysql
database.host = localhost
database.database = testdb
database.username = root
database.password = 123456
database.charset = utf8
database.collation = utf8_general_ci
database.prefix = ''
```

#### 与框架集成

```
// application/Bootstrap.php
    /**
     * 初始化Loader
     */
    public function _initLoader()
    {
        $loader = Loader::getInstance();

        $autoload = APP_ROOT . '/vendor/autoload.php';
        if (file_exists($autoload)) {
            $loader->import($autoload);
        }
    }

    /**
     * 初始化 Eloquent ORM
     *
     * @param Dispatcher|\Yaf\Dispatcher $dispatcher
     */
    public function _initDbAdapter(Dispatcher $dispatcher)
    {
        $capsule = new Capsule();
        $db = $this->config['database'];
        $capsule->addConnection($db);
        $capsule->setEventDispatcher(new LDispatcher(new LContainer));
        $capsule->setAsGlobal();
        $capsule->bootEloquent();

        // 为类创建别名，便于在脚本或者其他地方直接通过DB::的形式来直接使用
        class_alias('\Illuminate\Database\Capsule\Manager', 'DB');
    }
```

#### 示例

```
<?php

use Illuminate\Database\Eloquent\SoftDeletes;
use Illuminate\Database\Eloquent\Model;

class UserModel extends Model
{

}
```

具体使用可以参考Laravel的官方使用示例:[Eloquent ORM](https://laravel.com/docs/5.5/eloquent)


