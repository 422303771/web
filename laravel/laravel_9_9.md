<!--toc-->

- [9.9 错误&日志](#99-错误日志)
	- [9.9.1 简介](#991-简介)
	- [9.9.2 配置](#992-配置)
	- [9.9.3 异常处理器](#993-异常处理器)
		- [9.9.3.1 report方法](#9931-report方法)
		- [9.9.3.2 render方法](#9932-render方法)
	- [9.9.4 HTTP异常](#994-http异常)
		- [9.9.4.1 自定义HTTP错误页面](#9941-自定义http错误页面)
	- [9.9.5 日志](#995-日志)
- [9.10 事件](#910-事件)
	- [9.10.1 简介](#9101-简介)
	- [9.10.2 注册事件/监听器](#9102-注册事件监听器)
		- [9.10.2.1 生成事件/监听器类](#91021-生成事件监听器类)
		- [9.10.2.2 手动注册事件](#91022-手动注册事件)
	- [9.10.3 定义事件](#9103-定义事件)
	- [9.10.4 定义监听器](#9104-定义监听器)
		- [9.10.4.1 事件监听器队列](#91041-事件监听器队列)
	- [9.10.5 触发事件](#9105-触发事件)
	- [9.10.6 广播事件](#9106-广播事件)
		- [9.10.6.1 配置](#91061-配置)
		- [9.10.6.2 将事件标记为广播](#91062-将事件标记为广播)
		- [9.10.6.3 广播数据](#91063-广播数据)
		- [9.10.6.4 自定义事件广播](#91064-自定义事件广播)
		- [9.10.6.5 消费事件广播](#91065-消费事件广播)
	- [9.10.7 事件订阅者](#9107-事件订阅者)

<!-- tocstop -->

---

# 9.9 错误&日志

## 9.9.1 简介

Laravel默认已经为我们配置好了错误和异常处理，此外，`Laravel`还集成了`Monolog`日志库以便提供多种功能强大的日志处理器。

--------------------------------------------------------------------------------

## 9.9.2 配置

- **错误详情显示**

  配置文件`config/app.php`中的`debug`配置选项控制浏览器显示的错误详情数量。默认情况下，该配置选项被设置在`.env`文件中的环境变量`APP_DEBUG`。

  对本地开发而言，你应该设置环境变量`APP_DEBUG`值为`true`。在生产环境，该值应该被设置为`false`。

- **日志模式**

  `Laravel`支持日志方法`single`, `daily`, `syslog` 和 `errorlog`。

  例如，如果你想要日志文件按日生成而不是生成单个文件，应该在配置文件`config/app.php`中设置log值如下：

  ```
    'log' => 'daily'
  ```

- **自定义Monolog配置**

  如果你想要在应用中完全控制`Monolog`的配置，可以使用应用的`configureMonologUsing`方法。你应该在`bootstrap/app.php`文件返回`$app`变量之前调用该方法：

  ```php
    $app->configureMonologUsing(function($monolog) {
        $monolog->pushHandler(...);
    });

    return $app;
  ```

--------------------------------------------------------------------------------

## 9.9.3 异常处理器

所有异常都由类`App\Exceptions\Handler`处理，该类包含两个方法：`report`和`render`。下面我们详细阐述这两个方法。

### 9.9.3.1 report方法

`report`方法用于记录异常并将其发送给外部服务如`Bugsnag`。

默认情况下，`report`方法只是将异常传递给异常记录的基类，你可以随心所欲的记录异常。

例如，如果你需要以不同方式报告不同类型的异常，可使用PHP的`instanceof`比较操作符：

```php
/**
 * 报告或记录异常
 *
 * This is a great spot to send exceptions to Sentry, Bugsnag, etc.
 *
 * @param  \Exception  $e
 * @return void
 */
public function report(Exception $e){
    if ($e instanceof CustomException) {
        //
    }

    return parent::report($e);
}
```

- **通过类型忽略异常**

  异常处理器的`$dontReport`属性包含一个不会被记录的异常类型数组，默认情况下，`404错误`异常不会被写到日志文件，如果需要的话你可以添加其他异常类型到这个数组。

### 9.9.3.2 render方法

`render`方法负责将给定异常转化为发送给浏览器的HTTP响应，默认情况下，异常被传递给为你生成响应的基类。然而，你可以随心所欲地检查异常类型或者返回自定义响应：

```php
/**
 * 将异常渲染到HTTP响应中
 *
 * @param  \Illuminate\Http\Request  $request
 * @param  \Exception  $e
 * @return \Illuminate\Http\Response
 */
public function render($request, Exception $e){
    if ($e instanceof CustomException) {
        return response()->view('errors.custom', [], 500);
    }

    return parent::render($request, $e);
}
```

--------------------------------------------------------------------------------

## 9.9.4 HTTP异常

有些异常描述来自服务器的HTTP错误码，例如，这可能是一个"页面未找到"错误（`404`），"认证失败错误"（`401`）亦或是程序出错造成的`500`错误，为了在应用中生成这样的响应，使用如下方法：

```
abort(404);
```

`abort`方法会立即引发一个会被异常处理器渲染的异常，此外，你还可以像这样提供响应描述：

```
abort(403, 'Unauthorized action.');
```

该方法可在请求生命周期的任何时间点使用。

### 9.9.4.1 自定义HTTP错误页面

Laravel使得返回多种`HTTP`状态码的错误页面变得简单。

例如，如果你想要自定义`404`错误页面，创建一个`resources/views/errors/404.blade.php`文件，该文件将会渲染程序生成的所有`404`错误。

改目录下的视图命名应该和相应的HTTP状态码相匹配。

--------------------------------------------------------------------------------

## 9.9.5 日志

Laravel日志工具基于强大的`Monolog`库，默认情况下，Laravel被,配置为在`storage/logs`目录下每日为应用生成日志文件，你可以使用`Log`门面编写日志信息到日志中：

```php
<?php

namespace App\Http\Controllers;

use Log;
use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 显示指定用户的属性
     *
     * @param  int  $id
     * @return Response
     */
    public function showProfile($id)
    {
        Log::info('Showing user profile for user: '.$id);
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```

该日志记录器提供了`RFC 5424`中定义的八种日志级别：

`emergency`, `alert`, `critical`, `error`,`warning`, `notice`, `info` 和 `debug`。

```
Log::emergency($error);
Log::alert($error);
Log::critical($error);
Log::error($error);
Log::warning($error);
Log::notice($error);
Log::info($error);
Log::debug($error);
```

- **上下文信息**

  上下文数据数组也会被传递给日志方法。上下文数据将会和日志消息一起被格式化和显示：

  ```
    Log::info('User failed to login.', ['id' => $user->id]);
  ```

- **访问底层Monolog实例**

  `Monolog`有多个可用于日志的处理器，如果需要的话，你可以访问底层`Monolog`实例：

  ```
    $monolog = Log::getMonolog();
  ```

--------------------------------------------------------------------------------

# 9.10 事件

## 9.10.1 简介

Laravel 事件提供了简单的观察者模式实现，允许你订阅和监听应用中的事件。事件类通常存放在 `app/Events`目录，监听器存放在`app/Listeners`。

--------------------------------------------------------------------------------

## 9.10.2 注册事件/监听器

Laravel 自带的`EventServiceProvider`为事件注册提供了方便之所。其中的`listen`属性包含了事件（键）和对应监听器（值）数组。如果应用需要，你可以添加多个事件到该数组。

例如，让我们添加 `PodcastWasPurchased`事件：

```php
/**
 * 事件监听器映射
 *
 * @var array
 */
protected $listen = [
    'App\Events\PodcastWasPurchased' => [
        'App\Listeners\EmailPurchaseConfirmation',
    ],
];
```

### 9.10.2.1 生成事件/监听器类

当然，手动为每个事件和监听器创建文件是很笨重的，取而代之地，我们可见简单添加监听器和事件到 `EventServiceProvider`然后使用`event:generate`命令。该命令将会生成罗列在`EventServiceProvider`中的所有事件和监听器。

当然，已存在的事件和监听器不会被创建：

```
php artisan event:generate
```

### 9.10.2.2 手动注册事件

一般我们需要将事件注册到`EventServiceProvider`的`$listen`数组，此外，我们还可以使用`Event`门面或者`Illuminate\Contracts\Events\Dispatcher`契约的具体实现类作为事件分发器手动注册事件：

```php
/**
 * Register any other events for your application.
 *
 * @param  \Illuminate\Contracts\Events\Dispatcher  $events
 * @return void
 */
public function boot(DispatcherContract $events)
{
    parent::boot($events);

    $events->listen('event.name', function ($foo, $bar) {
        //
    });
}
```

- **使用通配符作为事件监听器**

  你还可以使用通配符`*`来注册监听器，从而允许你通过同一个监听器捕获多个事件。通配符监听器接收整个事件数据数组作为参数：

  ```php
    $events->listen('event.*', function (array $data) {
        //
    });
  ```

--------------------------------------------------------------------------------

## 9.10.3 定义事件

事件类是一个处理与事件相关的简单数据容器，例如，假设我们生成的`PodcastWasPurchased`事件接收一个`Eloquent ORM`对象：

```php
<?php

namespace App\Events;

use App\Podcast;
use App\Events\Event;
use Illuminate\Queue\SerializesModels;

class PodcastWasPurchased extends Event{
    use SerializesModels;

    public $podcast;

    /**
     * 创建新的事件实例
     *
     * @param  Podcast  $podcast
     * @return void
     */
    public function __construct(Podcast $podcast)
    {
        $this->podcast = $podcast;
    }
}
```

正如你所看到的，该事件类不包含任何特定逻辑，只是一个存放被购买的`Podcast`对象的容器，如果事件对象被序列化的话，事件使用的`SerializesModels`trait 将会使用 PHP 的`serialize`函数序列化所有 Eloquent 模型。

--------------------------------------------------------------------------------

## 9.10.4 定义监听器

接下来，让我们看看我们的示例事件的监听器，事件监听器在`handle`方法中接收事件实例，`event:generate`命令将会自动在`handle`方法中导入合适的事件类和类型提示事件。

在`handle`方法内，你可以执行任何需要的逻辑以响应事件。

```php
<?php

namespace App\Listeners;

use App\Events\PodcastWasPurchased;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;

class EmailPurchaseConfirmation{
    /**
     * 创建事件监听器
     *
     * @return void
     */
    public function __construct()
    {
        //
    }

    /**
     * 处理事件
     *
     * @param  PodcastWasPurchased  $event
     * @return void
     */
    public function handle(PodcastWasPurchased $event)
    {
        // Access the podcast using $event->podcast...
    }
}
```

你的事件监听器还可以在构造器中类型提示任何需要的依赖，所有事件监听器通过服务容器解析，所以依赖会自动注入：

```php
use Illuminate\Contracts\Mail\Mailer;

public function __construct(Mailer $mailer){
    $this->mailer = $mailer;
}
```

- **停止事件继续往下传播**

  有时候，你希望停止事件被传播到其它监听器，你可以通过从监听器的`handle`方法中返回`false`来实现。

### 9.10.4.1 事件监听器队列

需要将事件监听器放到队列？没有比这更简单的了，只需要让监听器类实现`ShouldQueue`接口即可，通过 `Artisan`命令`event:generate`生成的监听器类已经将接口导入当前命名空间，所有你可以立即拿来使用：

```php
<?php

namespace App\Listeners;

use App\Events\PodcastWasPurchased;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;

class EmailPurchaseConfirmation implements ShouldQueue{
    //
}
```

就是这么简单，当监听器被事件调用，将会使用 Laravel 的队列系统通过队列分发器自动队列化。如果通过队列执行监听器的时候没有抛出任何异常，队列任务在执行完成后被自动删除。

- **手动访问队列**

  如果你需要手动访问底层队列任务的`delete`和`release`方法，在生成的监听器中默认导入的`Illuminate\Queue\InteractsWithQueue` trait 提供了访问这两个方法的权限：

  ```php
    <?php

    namespace App\Listeners;

    use App\Events\PodcastWasPurchased;
    use Illuminate\Queue\InteractsWithQueue;
    use Illuminate\Contracts\Queue\ShouldQueue;

    class EmailPurchaseConfirmation implements ShouldQueue{
        use InteractsWithQueue;

        public function handle(PodcastWasPurchased $event)
        {
            if (true) {
                $this->release(30);
            }
        }
    }
  ```

--------------------------------------------------------------------------------

## 9.10.5 触发事件

要触发一个事件，可以使用`Event`门面，传递一个事件实例到`fire`方法，`fire`方法会分发事件到所有监听器：

```php
<?php

namespace App\Http\Controllers;

use Event;
use App\Podcast;
use App\Events\PodcastWasPurchased;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 显示指定用户属性
     *
     * @param  int  $userId
     * @param  int  $podcastId
     * @return Response
     */
    public function purchasePodcast($userId, $podcastId)
    {
        $podcast = Podcast::findOrFail($podcastId);

        // Purchase podcast logic...

        Event::fire(new PodcastWasPurchased($podcast));
    }
}
```

此外，你还可以使用全局的辅助函数`event`来触发事件：

```php
event(new PodcastWasPurchased($podcast));
```

--------------------------------------------------------------------------------

## 9.10.6 广播事件

在很多现代 Web 应用中，Web 套接字被用于实现实时更新的用户接口。当一些数据在服务器上被更新，通常一条消息通过`websocket`连接被发送给客户端处理。

为帮助你构建这样的应用，`Laravel`让通过`websocket`连接广播事件变得简单。广播`Laravel`事件允许你在服务端和客户端`JavaScript`框架之间共享同一事件名。

### 9.10.6.1 配置

所有的事件广播配置选项都存放在`config/broadcasting.php`配置文件中。`Laravel`支持多种广播驱动：`Pusher`、`Redis`以及一个服务于本地开发和调试的日志驱动。每一个驱动都有一个配置示例。

- **广播预备知识**

  事件广播需要以下两个依赖：

  - Pusher: pusher/pusher-php-server ~2.0

  - Redis: predis/predis ~1.0

- **队列预备知识**

  在开始介绍广播事件之前，还需要配置并运行一个队列监听器。所有事件广播都通过队列任务来完成以便应用的响应时间不受影响。

### 9.10.6.2 将事件标记为广播

要告诉`Laravel`给定事件应该被广播，需要在事件类上实现 `Illuminate\Contracts\Broadcasting\ShouldBroadcast`接口。`ShouldBroadcast`接口要求你实现一个方法：`broadcastOn`。该方法应该返回事件广播"频道"名称数组：

```php
<?php

namespace App\Events;

use App\User;
use App\Events\Event;
use Illuminate\Queue\SerializesModels;
use Illuminate\Contracts\Broadcasting\ShouldBroadcast;

class ServerCreated extends Event implements ShouldBroadcast{
    use SerializesModels;

    public $user;

    /**
     * 创建新的事件实例
     *
     * @return void
     */
    public function __construct(User $user)
    {
        $this->user = $user;
    }

    /**
     * 获取事件广播频道
     *
     * @return array
     */
    public function broadcastOn()
    {
        return ['user.'.$this->user->id];
    }
}
```

然后，你只需要和正常一样触发该事件，事件被触发后，一个队列任务将通过指定广播驱动自动广播该事件。

### 9.10.6.3 广播数据

如果某个事件被广播，其所有的`public`属性都会按照事件负载自动序列化和广播，从而允许你从 `JavaScript`中访问所有`public`数据，因此，举个例子，如果你的事件有一个单独的包含`Eloquent`模型的 `$user`属性，广播负载定义如下：

```php
{
    "user": {
        "id": 1,
        "name": "Jonathan Banks"
        ...
    }
}
```

然而，如果你希望对广播负载有更加细粒度的控制，可以添加`broadcastWith`方法到事件，该方法应该返回你想要通过事件广播的数组数据：

```php
/**
 * 获取广播数据
 *
 * @return array
 */
public function broadcastWith(){
    return ['user' => $this->user->id];
}
```

### 9.10.6.4 自定义事件广播

- **自定义事件名**

  默认情况下，广播事件名就是事件类名，因此，如果事件的类名是`App\Events\ServerCreated`，对应的广播事件名就是`App\Events\ServerCreated`，你可以通过事件类上的`broadcastAs`方法自定义广播事件名：

  ```php
    /**
     * 获取广播事件名称
     *
     * @return string
     */
    public function broadcastAs()
    {
        return 'app.server-created';
    }
  ```

- **自定义队列**

  默认情况下，每个被广播的事件都位于配置文件`queue.php`中定义的默认队列连接中的默认队列中，你可以通过事件类的`onQueue`方法自定义广播事件的队列名称。该方法会返回你期望使用的队列名：

  ```php
    /**
     * 设置事件所在队列的名称
     *
     * @return string
     */
    public function onQueue()
    {
        return 'your-queue-name';
    }
  ```

### 9.10.6.5 消费事件广播

- **Pusher**

  你可以通过`Pusher`的`JavaScript SDK`方便地使用`Pusher`驱动消费事件广播。

  例如，让我们从之前的例子中消费`App\Events\ServerCreated`事件：

  ```php
    this.pusher = new Pusher('pusher-key');

    this.pusherChannel = this.pusher.subscribe('user.' + USER_ID);

    this.pusherChannel.bind('App\\Events\\ServerCreated', function(message) {
        console.log(message.user);
    });
  ```

- **Redis**

  如果你在使用`Redis`广播，你将需要编写自己的`Redis pub/sub`消费者来接收消息并使用自己选择的 `websocket`技术将其进行广播。

  例如，你可以选择使用`Node`编写的流行的`Socket.io`库。

  ```javascript
    var app = require('http').createServer(handler);
    var io = require('socket.io')(app);

    var Redis = require('ioredis');
    var redis = new Redis();

    app.listen(6001, function() {
        console.log('Server is running!');});

    function handler(req, res) {
        res.writeHead(200);
        res.end('');}

    io.on('connection', function(socket) {
        //
    });

    redis.psubscribe('*', function(err, count) {
        //
    });

    redis.on('pmessage', function(subscribed, channel, message) {
        message = JSON.parse(message);
        io.emit(channel + ':' + message.event, message.data);
    });
  ```

--------------------------------------------------------------------------------

## 9.10.7 事件订阅者

事件订阅者是指那些在类本身中订阅到多个事件的类，从而允许你在单个类中定义一些事件处理器。订阅者应该定义一个`subscribe`方法，该方法中传入一个事件分发器实例：

```php
<?php

namespace App\Listeners;

class UserEventListener{
    /**
     * 处理用户登录事件
     */
    public function onUserLogin($event) {}

    /**
     * 处理用户退出事件
     */
    public function onUserLogout($event) {}

    /**
     * 为订阅者注册监听器
     *
     * @param  Illuminate\Events\Dispatcher  $events
     * @return array
     */
    public function subscribe($events)
    {
        $events->listen(
            'App\Events\UserLoggedIn',
            'App\Listeners\UserEventListener@onUserLogin'
        );

        $events->listen(
            'App\Events\UserLoggedOut',
            'App\Listeners\UserEventListener@onUserLogout'
        );
    }

}
```

- **注册一个事件订阅者**

  订阅者被定义后，可以通过事件分发器进行注册，你可以使用`EventServiceProvider`上的`$subcribe`属性来注册订阅者。例如，让我们添加`UserEventListener`：

  ```php
    <?php

    namespace App\Providers;

    use Illuminate\Contracts\Events\Dispatcher as DispatcherContract;
    use Illuminate\Foundation\Support\Providers\EventServiceProvider as ServiceProvider;

    class EventServiceProvider extends ServiceProvider{
        /**
         * 事件监听器映射数组
         *
         * @var array
         */
        protected $listen = [
            //
        ];

        /**
         * 要注册的订阅者
         *
         * @var array
         */
        protected $subscribe = [
            'App\Listeners\UserEventListener',
        ];
    }
  ```

--------------------------------------------------------------------------------
