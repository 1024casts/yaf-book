# 对象注册表

## 简介

Yaf_Registry, 对象注册表(或称对象仓库)是一个用于在整个应用空间(application space)内存储对象和值的容器. 通过把对象存储在其中,我们可以在整个项目的任何地方使用同一个对象.这种机制相当于一种全局存储. 我们可以通过Yaf_Registry类的静态方法来使用对象注册表. 另外,由于该类是一个数组对象,你可以使用数组形式来访问其中的类方法.

> 在PHP5.3之后, 打开`yaf.use_namespace`的情况下, 也可以使用 `Yaf\Registry`

## 内置方法的使用

主要包含4个方法

```
Yaf_Registry {
    public static Yaf_Registry has ( string $name );
    public static Yaf_Registry get ( string $name );
    public static Yaf_Registry set ( string $name, mixed $value );
    public static Yaf_Registry del ( string $name );
}
```

### set

`public static Yaf_Registry Yaf_Registry::set( string  $name , mixed  $value );` 

往全局注册表添加一个新的项

示例

```
<?php
    // Yaf_Application::app()->getConfig()， 从conf/application获取配置
    $config = Yaf_Application::app()->getConfig();

    Yaf_Registry::set('config', $config);
?>
```

### get

`public static Yaf_Registry Yaf_Registry::get( string  $name );`

获取注册表中寄存的项, 成功返回要获取的注册项的值, 失败返回FALSE。
如果$name没有在配置文件中配置，则返回`NULL`

示例

```
<?php
    $config = Yaf_Application::app()->getConfig();
    Yaf_Registry::set('config', $config);

    /* 之后可以在任何地方获取到 */
    var_dump(Yaf_Registry::get("config"));
?>
```

### has

`public static Yaf_Registry Yaf_Registry::has( string  $name );`

查询某一项目是否存在于注册表中, 存在返回TRUE, 不存在返回FALSE

示例

```
<?php
     /** 存入 */
     Yaf_Registry::set('config', Yaf_Application::app()->getConfig());

     var_dump(Yaf_Registry::has("config")); // true
?>
```

### del

`public static Yaf_Registry Yaf_Registry::del( string  $name );`

删除存在于注册表中的名为$name的项, 成功返回TRUE, 失败返回FALSE
如果被删除的对象不存在，则返回`true`

示例

```
<?php
    $config = Yaf_Application::app()->getConfig();
    Yaf_Registry::set('config', $config);

    var_dump(Yaf_Registry::del("config")); //true
?>
```