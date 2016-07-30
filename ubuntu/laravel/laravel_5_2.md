
## 5.2 中间件


### 5.2.1 简介


laravel中间件提供一个方便的机制来过滤进入web应用的HTTP请求。

例如，laravel内置一个中间件来验证用户是否经过授权，如果用户没有经过授权，中间件会将用户重定向到登陆页面，否则如果用户经过授权，中间件就会允许请求继续往前进行下一步操作。

当然，除了认证之外，中间件还可以用来处理更多任务。

例如：CORS中间件可以为离开站点的响应添加合适的头，日记中间件可以记录所有进入站点的请求。

laravel框架自带了一些中间件，包括维护模式、认证、CSRF等等。所有的中间件都位于`App/Http/Middleware`目录中。

------

### 5.2.2 定义中间件

可以使用Artisan命令`make:middleware`创建一个新的中间件

	php artisan make:middleware OldMiddleware

其中的`OldMiddleware`可以为任意名称。

命令会下`app/Http/Middleware`目录下创建一个新的中间件`OldMiddlware`,在这个中间件中，设定只允许`age`大于`200`的才能访问路由，否则，将用户重新定向到主页：

```php
<?php

namespace App\Http\Middleware;

use Closure;

class OldMiddleware
{
    /**
     * 返回请求过滤器
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->input('age') <= 200) {
            return redirect('home');
        }

        return $next($request);
    }

}
```
如果`age<=200`,中间件会返回一个HTTP重定向到客户端；否则，请求会被传统下去。将请求往下传递可以通过调用回调函数`$next`并传入`$request`。

理解中间件最好的方式是将中间件看做HTTP请求，到达目标动作之前必须经过的`层`，每一层都会检查请求并且可以完全拒绝它。

#### 5.2.2.1 中间件之前/之后

一个中间件，在请求前执行还是请求后执行取决于中间件本身。

比如，下方中间件会在请求处理前执行一些任务：

```php
<?php

namespace App\Http\Middleware;

use Closure;

class OldMiddleware
{
    /**
     * 返回请求过滤器
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        if ($request->input('age') <= 200) {
            return redirect('home');
        }

        return $next($request);
    }

}
```

下方中间件会在请求处理后执行任务：

```php
<?php

namespace App\Http\Middleware;

use Closure;

class AfterMiddleware
{
    public function handle($request, Closure $next)
    {
        $response = $next($request);

        // 执行动作

        return $response;
    }
}
```

------

### 5.2.3 注册中间件

#### 5.2.3.1 全局中间件

如果想要中间件放在每个HTTP请求期间被执行，只需要将相应的中间件类，设置到`app/Http/Kernel.php`的`$middleware`属性中。

#### 5.2.3.2 为路由指派中间件

如果想要分配中间件到指定路由，先要在`app/Http/Kernel.php`文件中分配给中间件一个简写的`key`,默认情况下，该类的`$routeMiddleware`属性包含了laravel内置的入口中间件，添加自己的中间件只需要将其追加到后面并为其分配一个`key`:

```php
// 在 App\Http\Kernel 里中
protected $routeMiddleware = [
    'auth' => \App\Http\Middleware\Authenticate::class,
    'auth.basic' => \Illuminate\Auth\Middleware\AuthenticateWithBasicAuth::class,
    'guest' => \App\Http\Middleware\RedirectIfAuthenticated::class,
    'throttle' => \Illuminate\Routing\Middleware\ThrottleRequests::class,
];
```
中间件在 HTTP Kernel 中被定义后，可以在路由选项数组中使用 `$middleware` 键来指定该中间件：

```php
Route::get('admin/profile', ['middleware' => 'auth', function () {
    //
}]);

```
使用数组分配多个中间件到路由,使用数组方式排列:

```php
Route::get('/', ['middleware' => ['first', 'second'], function () {
    //
}]);
```
处理使用数组外，还可以使用`middleware`方法链方式定义路由：

```php
Route::get('/', function () {
    //
})->middleware(['first', 'second']);
```

#### 5.2.3.3 中间件群组

有时你可能想将多个中间件组成单一的键，让它可以简单的指派给路由。

这可以通过使用HTTP的`$middlewareGroups`实现。

laravel自带了`web`与`api`两个中间件组，包含可以应用到webUI和API路由的通用中间件：

```php
/**
 * 应用的路由中间件组
 *
 * @var array
 */
protected $middlewareGroups = [
    'web' => [
        \App\Http\Middleware\EncryptCookies::class,
        \Illuminate\Cookie\Middleware\AddQueuedCookiesToResponse::class,
        \Illuminate\Session\Middleware\StartSession::class,
        \Illuminate\View\Middleware\ShareErrorsFromSession::class,
        \App\Http\Middleware\VerifyCsrfToken::class,
    ],

    'api' => [
        'throttle:60,1',
        'auth:api',
    ],
];
```

中间件组可以被分配给路由和控制器动作，使用方法与单个中间件相同。

**注意：中间件组只是让指派多个中间件变得简单**

```php
Route::group(['middleware' => ['web']], function () {
    //
});
```

-------

### 5.2.4 中间件参数

中间件还可以接收额外的自定义参数。

例如，应用要先检查使用者是否有权限，后执行特定的操作。

可以建立`RoleMiddleware`来接收使用者名称作为额外的参数。

额外的中间件参数会在`$next`参数之后传入中间件:

*注意：下方代价中指` Closure $next`后*

```php
<?php

namespace App\Http\Middleware;

use Closure;

class RoleMiddleware
{
    /**
     * 运行请求过滤器
     *
     * @param \Illuminate\Http\Request $request
     * @param \Closure $next
     * @param string $role
     * @return mixed
     * translator http://laravelacademy.org
     */
    public function handle($request, Closure $next, $role)
    {
        if (! $request->user()->hasRole($role)) {
            // Redirect...
        }

        return $next($request);
    }

}
```

中间件参数可以在定义路由时通过`:`分隔中间件名和参数名来指定，多个中间件参数可以通过`,`分隔：

```php
Route::put('post/{id}', ['middleware' => 'role:editor', function ($id) {
    //
}]);
```

-------

### 5.2.5 可终止的中间件

有时中间件需要在HTTP响应发送到浏览器后做一些工作。

例如，让`Session`中间件在响应发送到浏览器后，将Session中的数据写到存储器中。

为了实现这个，定义一个终止中间件，并在其中使用`terminable`方法：

```php
<?php

namespace Illuminate\Session\Middleware;

use Closure;

class StartSession
{
    public function handle($request, Closure $next)
    {
        return $next($request);
    }

    public function terminate($request, $response)
    {
        // Store the session data...
    }
}
```
这个`terminate`方法要接受`$request`和`$response`。一旦使用终止中间，应将其添加到`Http Kernel`作为全局变量使用。

当调用中间件中的`terminate`方法时，laravel将从服务器容器中取出这个中间件的新实例。

如果想在调用`handle`和`terminate`使用同一中间件实例，需要使用`container(容器)`的`singleton`方法将中间注入到容器。

这里`实例`可以理解为数据包。

