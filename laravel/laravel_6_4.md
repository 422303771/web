<!--toc-->

- [6.4 服务容器](#64-服务容器)
	- [6.4.1 简介](#641-简介)
	- [6.4.2 绑定](#642-绑定)
		- [6.4.2.1 绑定接口到实现方法](#6421-绑定接口到实现方法)
		- [6.4.2.2 上下文绑定](#6422-上下文绑定)
		- [6.4.2.2 标签](#6422-标签)
	- [6.4.3 解析](#643-解析)
	- [6.4.4 容器事件](#644-容器事件)

<!-- tocstop -->

----

# 6.4 服务容器

## 6.4.1 简介

Laravel 服务容器是一个用于管理类依赖和执行依赖注入的强大工具。依赖注入听上去很花哨，其实质是通过构造函数或者某些情况下通过 `setter` 方法将类依赖注入到类中。

让我们看一个简单的例子：

```php
<?php

namespace App\Jobs;

use App\User;
use Illuminate\Contracts\Mail\Mailer;
use Illuminate\Contracts\Bus\SelfHandling;

class PurchasePodcast implements SelfHandling
{
    /**
     * mailer 的實作。
     */
    protected $mailer;

    /**
     * 建立一個新實例。
     *
     * @param  Mailer  $mailer
     * @return void
     */
    public function __construct(Mailer $mailer)
    {
        $this->mailer = $mailer;
    }

    /**
     * 購買 podcast。
     *
     * @return void
     */
    public function handle()
    {
        //
    }
}
```

在这个例子中，购买`podcast`时，`PurchasePodcast`任务会需要寄送`e-mails`。因此，将注入能寄送`e-mails`的服务。由于服务被注入，我们能容易地切换成其它实例。当测试应用程序时，我们能很容易的测试，或者建立假的实例。

深入了解laravel服务容器对构建大型laravel应用很重要，对于贡献代码到laravel核心也很有帮助(很少需要这样做。)

--------------------------------------------------------------------------------

## 6.4.2 绑定

几乎所有的`服务容器`绑定都是在`服务提供者`中注册。所以下方所有的例子将示范在该情况中使用容器。不过，如果类别没有任何依赖，那么就没有将类绑定到容器的必要。并不是需要告诉容器如果构建这些物件，因为它会透PHP的`reflection`自动解析`对象`。

在一个服务提供者中，可以通过 `$this->app` 变量访问容器，然后使用 `bind`方法注册一个绑定，该方法需要两个参数，第一个参数是我们想要注册的类名或接口名称，第二个参数是返回类的实例的闭包：

```php
$this->app->bind('HelpSpot\API', function ($app) {
    return new HelpSpot\API($app['HttpClient']);
});
```

注意，我们接受容器本身作为解析器的一个参数，然后我们可以使用该容器，来解析我们正在构建的对象的子依赖。

- **绑定一个单例**

  `singleton` 方法绑定，一个只需要解析一次的类或接口到容器，然后接下来对容器的调用将会返回同一个实例：

  ```php
    $this->app->singleton('FooBar', function ($app) {
        return new FooBar($app['SomethingElse']);
    });
  ```

- **绑定实例**

  你还可以使用 `instance` 方法绑定一个已存在的对象实例到容器，随后对容器的调用将总是返回给定的实例：

  ```php
    $fooBar = new FooBar(new SomethingElse);

    $this->app->instance('FooBar', $fooBar);
  ```

### 6.4.2.1 绑定接口到实现方法

服务容器的一个非常强大的特性是，其绑定接口到实现方法的能力。

我们假设有一个 `EventPusher` 接口及其 `RedisEventPusher` 实现方法，编写完该接口的 `RedisEventPusher` 实现方法后，就可以将其注册到服务容器：

```php
$this->app->bind('App\Contracts\EventPusher', 'App\Services\RedisEventPusher');
```

这段代码告诉容器当一个类需要 `EventPusher` 的实现方法将会注入 `RedisEventPusher`，现在我们可以在构造器或者任何其它通过服务容器注入依赖的地方进行 `EventPusher` 接口的类型提示：

```php
use App\Contracts\EventPusher;

/**
 * 创建一个新的类实例
 *
 * @param  EventPusher  $pusher
 * @return void
 */
public function __construct(EventPusher $pusher){
    $this->pusher = $pusher;
}
```

### 6.4.2.2 上下文绑定

有时侯我们可能有两个类使用同一个接口，但我们希望在每个类中注入不同实现方法。

例如，当系统接到一个新的订单的时候，我们想要通过`PubNub`而不是 `Pusher` 发送一个事件。Laravel 定义了一个简单、平滑的方式来定义这种行为：

```php
$this->app->when('App\Handlers\Commands\CreateOrderHandler')
          ->needs('App\Contracts\EventPusher')
          ->give('App\Services\PubNubEventPusher');
```

你甚至还可以传递一个闭包到 `give` 方法：

```php
$this->app->when('App\Handlers\Commands\CreateOrderHandler')
    ->needs('App\Contracts\EventPusher')
    ->give(function () {
        // Resolve dependency...
    });
```

- **绑定原始值**

  有时候你可能有一个获取若干注入类的类，但还需要一个注入的原始值，比如整型数据，你可以轻松使用上下文绑定来注入指定类所需要的任何值：

  ```php
    $this->app->when('App\Handlers\Commands\CreateOrderHandler')
        ->needs('$maxOrderCount')
        ->give(10);
  ```

### 6.4.2.2 标签

有些情况下，需要解析特定分类下的所有绑定。

例如：你正在构建一个报表规整器，要通过接收有多个不同`Report`接口数组实现，在注册完 `Report` 实现方法之后，可以通过`tag`方法给它们分配一个标签：

将`SpeedReport`与`MemoryReport`打上名为`reports`的标签。

```php
$this->app->bind('SpeedReport', function () {
    //
});

$this->app->bind('MemoryReport', function () {
   //
});

$this->app->tag(['SpeedReport', 'MemoryReport'], 'reports');
```

这些服务被打上标签后，可以通过 `tagged` 方法来轻松解析它们：

```php
    $this->app->bind('ReportAggregator', function ($app) {
    return new ReportAggregator($app->tagged('reports'));
});
```

--------------------------------------------------------------------------------

## 6.4.3 解析

有很多方式可以从容器中解析对象，首先，你可以使用 `make` 方法，该方法接收你想要解析的类名或接口名作为参数：

```
$fooBar = $this->app->make('FooBar');
```

其次，你可以以数组方式访问容器，因为其实现了 PHP 的 `ArrayAccess` 接口：

```
$fooBar = $this->app['FooBar'];
```

最后，也是最常用的，你可以简单的通过,在类的构造函数中,对依赖进行类型提示,来从容器中解析对象.包括控制器、事件监听器、队列任务、中间件等都是通过这种方式。在实践中，这是大多数对象从容器中解析的方式。

容器会自动为其解析类注入依赖.

例如：你可以在控制器的构造函数中为应用定义的仓库进行类型提示，该仓库会自动解析并注入该类。

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Routing\Controller;
use App\Users\Repository as UserRepository;

class UserController extends Controller{
    /**
     * 用户仓库实例
     */
    protected $users;

    /**
     * 创建一个控制器实例
     *
     * @param  UserRepository  $users
     * @return void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }

    /**
     * 通过指定ID显示用户
     *
     * @param  int  $id
     * @return Response
     */
    public function show($id)
    {
        //
    }
}
```

--------------------------------------------------------------------------------

## 6.4.4 容器事件

服务容器在每一次解析对象时都会触发一个事件，可以使用 resolving 方法监听该事件：

```php
$this->app->resolving(function ($object, $app) {
    // 容器解析所有类型对象时调用
});

$this->app->resolving(function (FooBar $fooBar, $app) {
    // 容器解析“FooBar”对象时调用
});
```

正如你所看到的，被解析的对象将会传递给回调，从而允许你在对象被传递给消费者之前为其设置额外属性。

--------------------------------------------------------------------------------
