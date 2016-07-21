## 6.3 服务提交者

### 6.3.1 简介

服务提供者是所有laravel应用启动的中心，你自己的应用以及所有Laravel的核心服务都是通过服务提供者启动。

但是，我们所谓的`启动`指的是什么？通常，这意味着注册事物，包括注册服务容器绑定，事件监听器，中间件甚至路由。服务提供者是应用配置的中心。

如果你打开Laravel自带的`config/app.php`文件，将会看到一个`providers`数组，这里就是应用所要加载的所有服务提供者类，当然，其中很多是延迟加载的，也就是说不是每次请求都会被加载，只有真的用到它们的时候才会加载。

本章里你将会学习如何编写自己的服务提供者并在Laravel应用中注册它们。

----

### 6.3.2 编写服务提供者

所以得服务提供者继承自`Illuminate\Support\ServiceProvider`类。继承该抽象类要求至少在服务提供者中定义一个方法：`register`。在`register`方法内，你唯一要做的事情就是绑事物到服务容器，不要尝试在其中注册任何时间监听器，路由或者任何其它功能。

通过Artisan命令`make:provider`可以简单生成一个新的提供者：

	php artisan make:provider RiakServiceProvider

文件被创建到`App/Providers/RiakServiceProvider.php`

#### 6.3.2.1 register方法

正如前面所提到的，在`register`方法中只绑定事物到服务容器，而不要做其它事情，否则，一不小心就会用到一个尚未被加载的服务提供者提供的服务。

现在来看看一个基本的服务提供者是什么样：

```php
<?php

namespace App\Providers;

use Riak\Connection;
use Illuminate\Support\ServiceProvider;

class RiakServiceProvider extends ServiceProvider{
    /**
     * 在容器中注册绑定.
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton('Riak\Contracts\Connection', function ($app) {
            return new Connection(config('riak'));
        });
    }
}
``` 
编辑的文件为`App/Providers/RiakServiceProvider.php`

这个服务提供者只定义了一个`register`方法，并在服务容器中使用此方法定义了一份`Riak/connection`的实例。如果不清楚它是如何运作的，查看下一章节`服务容器`。

#### 6.3.2.2 boot方法

如果我们想要在服务提供者中注册视图composer该怎么做？这就要用到`boot`方法，该方法在所有服务提供者被注册以后才会被调用，这就是说我们可以在其中访问框架已注册的所有其他服务：

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class EventServiceProvider extends ServiceProvider{
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        view()->composer('view', function () {
            //
        });
    }

    /**
     * 在容器中注册绑定.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

* **boot方法的依赖注入**

我们可以在`boot`方法中类型提升依赖，服务容器会自动注册你需要的依赖：

```php
use Illuminate\Contracts\Routing\ResponseFactory;

public function boot(ResponseFactory $factory){
    $factory->macro('caps', function ($value) {
        //
    });
}
```

-----

### 6.3.3 注册服务提供者

所有服务提供者都是通过配置文件`config/app.php`中进行注册，该文件包含了一个列出所有服务提供者名字的`providers`数组，默认情况下，其中列出了所有核心服务提供者，这些服务提供者启动Laravel核心组件，比如邮件、列队、缓存等等。

要注册你自己的服务提供者，只需要将其追加到该数组中：

```php
'providers' => [
     // 其它服务提供者
     App\Providers\AppServiceProvider::class,
],
```

-----

### 6.3.4 延迟加载服务提供者

如果你的提供者仅仅只是在服务容器中注册绑定，你可以选择在延迟加载该绑，定直到注册绑定真的需要时再加载，延迟加载这样的一个提供者将会提升应用的性能，因为它不会在每次请求时都从文件系统加载。

想要延迟加载一个提供者，设置defer属性为true并定义一个provides方法，该方法返回该提供者注册的服务容器绑定：

想要延迟加载一个提供者，设置`defer`属性为`true`并定义一个`provides`方法，该方法返回该提供者注册的服务容器绑定：

```php
<?php

namespace App\Providers;

use Riak\Connection;
use Illuminate\Support\ServiceProvider;

class RiakServiceProvider extends ServiceProvider{
    /**
     * 服务提供者加是否延迟加载.
     *
     * @var bool
     */
    protected $defer = true;

    /**
     * 注册服务提供者
     *
     * @return void
     */
    public function register()
    {
        $this->app->singleton('Riak\Contracts\Connection', function ($app) {
            return new Connection($app['config']['riak']);
        });
    }

    /**
     * 获取由提供者提供的服务.
     *
     * @return array
     */
    public function provides()
    {
        return ['Riak\Contracts\Connection'];
    }

}
```
Laravel编译并保存所有延迟服务者提供的服务以及服务提供者的类名。

然后，只有当你尝试解析其中某个服务时Laravel才会加载其服务提交者。

-----