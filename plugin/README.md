# 插件

## 简介

Yaf支持用户定义插件来扩展Yaf的功能, 这些插件都是一些类. 它们都必须继承自Yaf_Plugin_Abstract. 插件要发挥功效, 也必须现实的在Yaf中进行注册, 然后在适当的实际, Yaf就会调用它.

## 支持的hook

Yaf框架定义了6个Hook, 这6个hook会依次被调用，分别是

|hook名称|触发顺序|触发时机|说明|
|-|:-:|:-|:-|
|routerStartup|1|在路由之前触发|这个是6个事件中, 最早的一个. 但是一些全局自定的工作, 还是应该放在Bootstrap中去完成|
|routerShutdown|2|路由结束之后触发|此时路由一定正确完成, 否则这个事件不会触发|
|dispatchLoopStartup|3|分发循环开始之前被触发||
|preDispatch|4|分发之前触发|如果在一个请求处理过程中, 发生了forward, 则这个事件会被触发多次|
|postDispatch|5|分发结束之后触发|此时动作已经执行结束, 视图也已经渲染完成. 和preDispatch类似, 此事件也可能触发多次|
|dispatchLoopShutdown|6|分发循环结束之后触发|此时表示所有的业务逻辑都已经运行完成, 但是响应还没有发送|


## 插件的定义

一般情况下, 插件应该放置在`application`下的plugins目录, 这样在自动加载的时候, 加载器通过类名, 发现这是个插件类, 就会在这个目录下查找.
当然, 插件也可以放在任何你想放置的地方, 只要你能把这个类加载进来就可以。

插件类是用户编写的, 但是它需要继承自`Yaf_Plugin_Abstract`. 对于插件来说, 这6个Hook, 它不需要全部关心, 它只需要在插件类中定义和上面事件同名的方法, 那么这个方法就会在该事件触发的时候被调用.

而插件方法, 可以接受俩个参数, Yaf_Request_Abstract实例和Yaf_Response_Abstract实例. 一个插件类例子如下:

```
<?php
class UserPlugin extends Yaf_Plugin_Abstract 
{

    public function routerStartup(Yaf_Request_Abstract $request, Yaf_Response_Abstract $response) 
    {

    }

    public function routerShutdown(Yaf_Request_Abstract $request, Yaf_Response_Abstract $response) 
    {

    }
}
```

> 这个例子中, 插件UserPlugin只关心两个事件. 所以就定义了两个方法.

## 注册插件

插件要生效, 还需要向`Yaf_Dispatcher`注册, 那么一般的插件的注册都会放在`Bootstrap`中进行. 一个注册插件的例子如下:

```
<?php
class Bootstrap extends Yaf_Bootstrap_Abstract{

    public function _initPlugin(Yaf_Dispatcher $dispatcher) 
    {
        $user = new UserPlugin();
        $dispatcher->registerPlugin($user);
    }
}
```

## 实战： 通过插件记录MySQL执行日志
