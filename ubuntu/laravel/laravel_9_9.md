## 9.9 错误&日志

### 9.9.1 简介

Laravel默认已经为我们配置好了错误和异常处理，此外，`Laravel`还集成了`Monolog`日志库以便提供多种功能强大的日志处理器。

-----

### 9.9.2 配置

* **错误详情显示**
	
	配置文件`config/app.php`中的`debug`配置选项控制浏览器显示的错误详情数量。默认情况下，该配置选项被设置在`.env`文件中的环境变量`APP_DEBUG`。
	
	对本地开发而言，你应该设置环境变量`APP_DEBUG`值为`true`。在生产环境，该值应该被设置为`false`。
	
* **日志模式**

	`Laravel`支持日志方法`single`, `daily`, `syslog` 和 `errorlog`。
	
	例如，如果你想要日志文件按日生成而不是生成单个文件，应该在配置文件`config/app.php`中设置log值如下：
	
	```
	'log' => 'daily'
	```

* **自定义Monolog配置**

	如果你想要在应用中完全控制`Monolog`的配置，可以使用应用的`configureMonologUsing`方法。你应该在`bootstrap/app.php`文件返回`$app`变量之前调用该方法：

	```php
	$app->configureMonologUsing(function($monolog) {
	    $monolog->pushHandler(...);
	});
	
	return $app;
	```

-----

### 9.9.3 异常处理器

所有异常都由类`App\Exceptions\Handler`处理，该类包含两个方法：`report`和`render`。下面我们详细阐述这两个方法。

#### 9.9.3.1 report方法

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

* **通过类型忽略异常**

	异常处理器的`$dontReport`属性包含一个不会被记录的异常类型数组，默认情况下，`404错误`异常不会被写到日志文件，如果需要的话你可以添加其他异常类型到这个数组。

#### 9.9.3.2 render方法

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

----

### 9.9.4 HTTP异常

有些异常描述来自服务器的HTTP错误码，例如，这可能是一个“页面未找到”错误（`404`），“认证失败错误”（`401`）亦或是程序出错造成的`500`错误，为了在应用中生成这样的响应，使用如下方法：

```
abort(404);
```
`abort`方法会立即引发一个会被异常处理器渲染的异常，此外，你还可以像这样提供响应描述：

```
abort(403, 'Unauthorized action.');
```
该方法可在请求生命周期的任何时间点使用。


#### 9.9.4.1 自定义HTTP错误页面

Laravel使得返回多种`HTTP`状态码的错误页面变得简单。

例如，如果你想要自定义`404`错误页面，创建一个`resources/views/errors/404.blade.php`文件，该文件将会渲染程序生成的所有`404`错误。

改目录下的视图命名应该和相应的HTTP状态码相匹配。

-----

### 9.9.5 日志

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

* **上下文信息**

	上下文数据数组也会被传递给日志方法。上下文数据将会和日志消息一起被格式化和显示：
	
	```
	Log::info('User failed to login.', ['id' => $user->id]);
	```

* **访问底层Monolog实例**

	`Monolog`有多个可用于日志的处理器，如果需要的话，你可以访问底层`Monolog`实例：
	
	```
	$monolog = Log::getMonolog();
	```

-----


## 9.10 事件

### 9.10.1 简介

Laravel 事件提供了简单的观察者模式实现，允许你订阅和监听应用中的事件。事件类通常存放在 `app/Events`目录，监听器存放在`app/Listeners`。

-----

### 9.10.2 注册事件/监听器

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

#### 9.10.2.1 生成事件/监听器类

当然，手动为每个事件和监听器创建文件是很笨重的，取而代之地，我们可见简单添加监听器和事件到 `EventServiceProvider`然后使用`event:generate`命令。该命令将会生成罗列在`EventServiceProvider`中的所有事件和监听器。

当然，已存在的事件和监听器不会被创建：

```
php artisan event:generate
```

#### 9.10.2.2 手动注册事件

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


* **使用通配符作为事件监听器**

	你还可以使用通配符`*`来注册监听器，从而允许你通过同一个监听器捕获多个事件。通配符监听器接收整个事件数据数组作为参数：
	
	```php
	$events->listen('event.*', function (array $data) {
	    //
	});
	```
	
-----

### 9.10.3 定义事件


-----

### 9.10.4 定义监听器

* **停止事件继续往下传播**

#### 9.10.4.1 事件监听器队列

* **手动访问队列**


----

### 9.10.5 触发事件


----

### 9.10.6 广播事件

#### 9.10.6.1 配置

* **广播预备知识**

* **队列预备知识**

#### 9.10.6.2 将事件标记为广播

#### 9.10.6.3 广播数据

#### 9.10.6.4 自定义事件广播

* **自定义事件名**

* **自定义队列**

#### 9.10.6.5 消费事件广播

* **Pusher**

* **Redis**


-----

### 9.10.7 事件订阅者

* **注册一个事件订阅者**


------