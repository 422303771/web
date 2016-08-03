# 6.6 合约

## 6.6.1 简介

Laravel的合约是一组由框架(framework)提供的核心服务接口。

例如：

`Illuminate\Contracts\Queue\Queue`合约规定，列队工作需要的方法。

`Illuminate\Contracts\Mail\Mailer`合约规定，发送电子邮件的方法。

每个合约都有框架提供的，相应的实现方法。

例如：

Laravel提供一个列队实现方法与各种驱动，和一个由`SwiftMailer`提供的邮件接收服务。

所有的Laravel合约都在各自的`github`仓库中更新。这为所有的合约，提供了一个快速参考点，以及一个单一的分离包，用于包开发。

[Github参考点](https://github.com/illuminate/contracts)。

### 6.6.1.1 合约Vs.门面

Laravel的门面提供一种简单的方法，利用Laravel服务无须输入和提示。这好于使用需要服务容器的合约方法。

但是，使用合约运行你定义依赖类。多余大多数应用，使用门面就好了。

但是，如果你真有的需要额外的松耦合，合约就可以提供，阅读下面的了解更多。

## 6.6.2 为什么用合约？

关于合约你可能有几个问题要问。为什么全部使用接口？不使用接口会更复杂？

下方是使用接口的重要原因：`松耦合`与`简洁性`

### 6.6.2.1 松耦合

首先，让我们来看一个使用紧耦合缓存实现方法的代码：

```php
<?php

namespace App\Orders;

class Repository
{
    /**
     * The cache instance.
     */
    protected $cache;

    /**
     * Create a new repository instance.
     *
     * @param  \SomePackage\Cache\Memcached  $cache
     * @return void
     */
    public function __construct(\SomePackage\Cache\Memcached $cache)
    {
        $this->cache = $cache;
    }

    /**
     * Retrieve an Order by ID.
     *
     * @param  int  $id
     * @return Order
     */
    public function find($id)
    {
        if ($this->cache->has($id))    {
            //
        }
    }
}
```

这这个类中，代码是使用紧耦合，到给定的缓存实现方法。

因为是从一个包中取得缓存，所以这是一个紧耦合。

如果这个包的API发生了改变，我们的代码也要随之改变。

同样的，如果我们要使用其他技术(例如：redis)，取代正在使用的技术(Memcached),我们还是要修改代码。

我们的类不应该用这些设定，例如，谁为他们提供数据，它是如何提供的等等。

使用下方松耦合方法，不需要绑定注入，进而优化代码：

```php
<?php

namespace App\Orders;

use Illuminate\Contracts\Cache\Repository as Cache;

class Repository
{
    /**
     * The cache instance.
     */
    protected $cache;

    /**
     * Create a new repository instance.
     *
     * @param  Cache  $cache
     * @return void
     */
    public function __construct(Cache $cache)
    {
        $this->cache = $cache;
    }
}
```

现在代码没有连接任何的服务提供者，甚至Laravel。由于合约包中没有实现方法也没有依赖关系，你可以很容易的替换合约，更换缓存技术，而无需修改代码。

### 6.6.2.2 简洁性

当所有的Laravel的服务，在简单接口中整洁的定义，就可以很容易的给定服务提供的功能。

合约服务为框架功能提了简洁的文档。

当你使用简单接口时，你的代码更容易理解和维护。

## 6.6.3 合约参考

合约                                                                                                                                | 参考的门面
:-------------------------------------------------------------------------------------------------------------------------------- | :----------------
[Illuminate\Contracts\Auth\Factory](https://github.com/illuminate/contracts/blob/master/Auth/Factory.php)                         | Auth
[Illuminate\Contracts\Auth\PasswordBroker](https://github.com/illuminate/contracts/blob/master/Auth/PasswordBroker.php)           | Password
[Illuminate\Contracts\Bus\Dispatcher](https://github.com/illuminate/contracts/blob/master/Bus/Dispatcher.php)                     | Bus
[Illuminate\Contracts\Broadcasting\Broadcaster](https://github.com/illuminate/contracts/blob/master/Broadcasting/Broadcaster.php) |
[Illuminate\Contracts\Cache\Repository](https://github.com/illuminate/contracts/blob/master/Cache/Repository.php)                 | Cache
[Illuminate\Contracts\Cache\Factory](https://github.com/illuminate/contracts/blob/master/Cache/Factory.php)                       | Cache::driver()
[Illuminate\Contracts\Config\Repository](https://github.com/illuminate/contracts/blob/master/Config/Repository.php)               | Config
[Illuminate\Contracts\Container\Container](https://github.com/illuminate/contracts/blob/master/Container/Container.php)           | App
[Illuminate\Contracts\Cookie\Factory](https://github.com/illuminate/contracts/blob/master/Cookie/Factory.php)                     | Cookie
[Illuminate\Contracts\Cookie\QueueingFactory](https://github.com/illuminate/contracts/blob/master/Cookie/QueueingFactory.php)     | Cookie::queue()
[Illuminate\Contracts\Encryption\Encrypter](https://github.com/illuminate/contracts/blob/master/Encryption/Encrypter.php)         | Crypt
[Illuminate\Contracts\Events\Dispatcher](https://github.com/illuminate/contracts/blob/master/Events/Dispatcher.php)               | Event
[Illuminate\Contracts\Filesystem\Cloud](https://github.com/illuminate/contracts/blob/master/Filesystem/Cloud.php)                 |
[Illuminate\Contracts\Filesystem\Factory](https://github.com/illuminate/contracts/blob/master/Filesystem/Factory.php)             | File
[Illuminate\Contracts\Filesystem\Filesystem](https://github.com/illuminate/contracts/blob/master/Filesystem/Filesystem.php)       | File
[Illuminate\Contracts\Foundation\Application](https://github.com/illuminate/contracts/blob/master/Foundation/Application.php)     | App
[Illuminate\Contracts\Hashing\Hasher](https://github.com/illuminate/contracts/blob/master/Hashing/Hasher.php)                     | Hash
[Illuminate\Contracts\Logging\Log](https://github.com/illuminate/contracts/blob/master/Logging/Log.php)                           | Log
[Illuminate\Contracts\Mail\MailQueue](https://github.com/illuminate/contracts/blob/master/Mail/MailQueue.php)                     | Mail::queue()
[Illuminate\Contracts\Mail\Mailer](https://github.com/illuminate/contracts/blob/master/Mail/Mailer.php)                           | Mail
[Illuminate\Contracts\Queue\Factory](https://github.com/illuminate/contracts/blob/master/Queue/Factory.php)                       | Queue::driver()
[Illuminate\Contracts\Queue\Queue](https://github.com/illuminate/contracts/blob/master/Queue/Queue.php)                           | Queue
[Illuminate\Contracts\Redis\Database](https://github.com/illuminate/contracts/blob/master/Redis/Database.php)                     | Redis
[Illuminate\Contracts\Routing\Registrar](https://github.com/illuminate/contracts/blob/master/Routing/Registrar.php)               | Route
[Illuminate\Contracts\Routing\ResponseFactory](https://github.com/illuminate/contracts/blob/master/Routing/ResponseFactory.php)   | Response
[Illuminate\Contracts\Routing\UrlGenerator](https://github.com/illuminate/contracts/blob/master/Routing/UrlGenerator.php)         | URL
[Illuminate\Contracts\Support\Arrayable](https://github.com/illuminate/contracts/blob/master/Support/Arrayable.php)               |
[Illuminate\Contracts\Support\Jsonable](https://github.com/illuminate/contracts/blob/master/Support/Jsonable.php)                 |
[Illuminate\Contracts\Support\Renderable](https://github.com/illuminate/contracts/blob/master/Support/Renderable.php)             |
[Illuminate\Contracts\Validation\Factory](https://github.com/illuminate/contracts/blob/master/Validation/Factory.php)             | Validator::make()
[Illuminate\Contracts\Validation\Validator](https://github.com/illuminate/contracts/blob/master/Validation/Validator.php)         |
[Illuminate\Contracts\View\Factory](https://github.com/illuminate/contracts/blob/master/View/Factory.php)                         | View::make()
[Illuminate\Contracts\View\View](https://github.com/illuminate/contracts/blob/master/View/View.php)                               |

## 6.6.4 如何使用合约

那么，如果使用一个合约的实现方法？这其实很简单。

许多类型的Laravel类，是通过服务容器解决的，包括控制器，事件监听，中间件，列队甚至是路由。

要使用合约，只需要进行类型提示，注册类的接口函数。

例如：下面的事件监听器：

```php
<?php

namespace App\Listeners;

use App\User;
use App\Events\NewUserRegistered;
use Illuminate\Contracts\Redis\Database;

class CacheUserInformation
{
    /**
     * The Redis database implementation.
     */
    protected $redis;

    /**
     * Create a new event handler instance.
     *
     * @param  Database  $redis
     * @return void
     */
    public function __construct(Database $redis)
    {
        $this->redis = $redis;
    }

    /**
     * Handle the event.
     *
     * @param  NewUserRegistered  $event
     * @return void
     */
    public function handle(NewUserRegistered $event)
    {
        //
    }
}
```

当事件监听器运行完成，服务容器将读取构造函数的类型提示，并注入相应的值。
