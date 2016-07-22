## 6.5 门面

### 6.5.1 简介

Facades为应用在服务容器中的绑定类，提供一个`静态`接口。

`Laravel`附带许多`facades`，你可以在不知道的情况下正在使用它们。

`Laravel`的门面作为服务容器中底层类的`静态代理`，相比于传统静态方法，在维护时能够提供更加易于测试，简明的语法。

----

### 6.5.2 使用门面

在Laravel应用的上下文中，门面是一个提供访问容器中对象的类。该机制原理由`Facade`类实现，Laravel自带的门面，以及创建的自定义门面，都会继承自`Illuminate\Support\Facades\Facade`基类。

门面类只需要实现一个方法：`getFacadeAccessor`。`getFacadeAccessor` 方法定义了从容器中解析什么，然后 `Facade` 基类使用魔术方法 `__callStatic()` 从你的门面中调用解析对象。

下面的例子中，将会调用Laravel的缓存系统，浏览代码后，也许你会觉得我们调用了`Cache`的静态方法`get`:

```php
<?php

namespace App\Http\Controllers;

use Cache;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 为指定用户显示属性
     *
     * @param  int  $id
     * @return Response
     */
    public function showProfile($id)
    {
        $user = Cache::get('user:'.$id);

        return view('profile', ['user' => $user]);
    }
}
```
注意我们在顶部位置引入`Cache`门面。该门面作为代理访问底层`Illuminate\Contracts\Cache\Factory`接口的实现方法。我们对门面的所有调用都会被传递给Laravel缓存服务的底层实例。

如果我们查看`Illuminate\Support\Facades\Cache`类的源码，将会发现其中并没有静态方法`get`:

```php
class Cache extends Facade{
    /**
     * 获取组件注册名称
     *
     * @return string
     */
    protected static function getFacadeAccessor() { 
        return 'cache'; 
    }
}
```
`Cache`门面继承`Facade`基类并定义了`getFacadeAccessor`方法，该方法的工作就是返回服务容器绑定类的别名，当用户引用`Cache`类的任何静态方法时，Laravel从服务容器中解析`Cache`绑定，然后在解析出的对象上调用所有请求方法(本例中是`get`)。

----

### 6.5.3 门面类列表

下面列出了每个门面及其对应的底层类，这对深入给定门面的API文档而言是个很有用的工具。服务容器绑定键也被包含进来：

|门面|类|服务器绑定别名|
|:----|:----|:----|
|App|[Illuminate\Foundation\Application](https://laravel.com/api/5.2/Illuminate/Foundation/Application.html)|app|
|Artisan|[Illuminate\Console\Application](https://laravel.com/api/5.2/Illuminate/Contracts/Console/Kernel.html)|artisan|
|Auth|[Illuminate\Auth\AuthManager](https://laravel.com/api/5.2/Illuminate/Auth/AuthManager.html)|auth|
|Blade|[Illuminate\View\Compilers\BladeCompiler](https://laravel.com/api/5.2/Illuminate/View/Compilers/BladeCompiler.html)|	blade.compiler|
|Bus|[Illuminate\Contracts\Bus\Dispatcher](https://laravel.com/api/5.2/Illuminate/Contracts/Bus/Dispatcher.html)||
|Cache|	[Illuminate\Cache\Repository](https://laravel.com/api/5.2/Illuminate/Cache/Repository.html)|cache|
|Config|[Illuminate\Config\Repository](https://laravel.com/api/5.2/Illuminate/Config/Repository.html)|config|
|Cookie|[Illuminate\Cookie\CookieJar](https://laravel.com/api/5.2/Illuminate/Cookie/CookieJar.html)|cookie|
|Crypt|[Illuminate\Encryption\Encrypter](https://laravel.com/api/5.2/Illuminate/Encryption/Encrypter.html)|encrypter|
|DB|[Illuminate\Database\DatabaseManager](https://laravel.com/api/5.2/Illuminate/Database/DatabaseManager.html)|db|
|DB (Instance)|[Illuminate\Database\Connection](https://laravel.com/api/5.2/Illuminate/Database/Connection.html)||
|Event|[Illuminate\Events\Dispatcher](https://laravel.com/api/5.2/Illuminate/Events/Dispatcher.html)|events|
|File|[Illuminate\Filesystem\Filesystem](https://laravel.com/api/5.2/Illuminate/Filesystem/Filesystem.html)|files|
|Gate|[Illuminate\Contracts\Auth\Access\Gate](https://laravel.com/api/5.1/Illuminate/Contracts/Auth/Access/Gate.html)||
|Hash|[Illuminate\Contracts\Hashing\Hasher](http://laravel.com/api/5.2/Illuminate/Contracts/Hashing/Hasher.html)|hash|
|Lang|[Illuminate\Translation\Translator](https://laravel.com/api/5.2/Illuminate/Translation/Translator.html)|translator|
|Log|[Illuminate\Log\Writer](https://laravel.com/api/5.2/Illuminate/Log/Writer.html)|log|
|Mail|[Illuminate\Mail\Mailer](https://laravel.com/api/5.2/Illuminate/Mail/Mailer.html)|mailer|
|Password|[Illuminate\Auth\Passwords\PasswordBroker](https://laravel.com/api/5.2/Illuminate/Auth/Passwords/PasswordBroker.html)|auth.password|
|Queue|[Illuminate\Queue\QueueManager](https://laravel.com/api/5.2/Illuminate/Queue/QueueManager.html)|queue|
|Queue (Instance)|	[Illuminate\Contracts\Queue\Queue](https://laravel.com/api/5.2/Illuminate/Contracts/Queue/Queue.html)|queue|
|Queue (Base Class)|[Illuminate\Queue\Queue](http://laravel.com/api/5.2/Illuminate/Queue/Queue.html)||
|Redirect|[Illuminate\Routing\Redirector](https://laravel.com/api/5.2/Illuminate/Routing/Redirector.html)|redirect|
|Redis|[Illuminate\Redis\Database](https://laravel.com/api/5.2/Illuminate/Redis/Database.html)|	redis|
|Request|[Illuminate\Http\Request](https://laravel.com/api/5.2/Illuminate/Http/Request.html)|	request|
|Response|[Illuminate\Contracts\Routing\ResponseFactory](https://laravel.com/api/5.2/Illuminate/Contracts/Routing/ResponseFactory.html)||
|Route|[Illuminate\Routing\Router](https://laravel.com/api/5.2/Illuminate/Routing/Router.html)|router|
|Schema|	[Illuminate\Database\Schema\Blueprint](https://laravel.com/api/5.2/Illuminate/Database/Schema/Blueprint.html)||
|Session|	[Illuminate\Session\SessionManager](https://laravel.com/api/5.2/Illuminate/Session/SessionManager.html)|session|
|Session (Instance)|[Illuminate\Session\Store](https://laravel.com/api/5.2/Illuminate/Session/Store.html)||
|Storage|[Illuminate\Contracts\Filesystem\Factory](https://laravel.com/api/5.2/Illuminate/Contracts/Filesystem/Factory.html)|filesystem|
|URL|	[Illuminate\Routing\UrlGenerator](https://laravel.com/api/5.2/Illuminate/Routing/UrlGenerator.html)|url|
|Validator|[Illuminate\Validation\Factory](https://laravel.com/api/5.2/Illuminate/Validation/Factory.html)|validator|
|Validator (Instance)|[Illuminate\Validation\Validator](https://laravel.com/api/5.2/Illuminate/Validation/Validator.html)||
|View|[Illuminate\View\Factory](https://laravel.com/api/5.2/Illuminate/View/Factory.html)|	view|
|View (Instance)|[Illuminate\View\View](https://laravel.com/api/5.2/Illuminate/View/View.html)||