# 5.5 响应

## 5.5.1 基本响应

所有路由和控制器都会返回某种被发送到用户浏览器的响应，laravel提供多种不同的方式来返回响应，最基本的响应就是从路由或控制器返回一个简单的字符串：

```php
Route::get('/', function () {
    return 'Hello World';
});
```

给定的字符串会被框架自动转化为HTTP响应。

### 5.5.1.1 Response对象

大多数路由和控制器动作都会返回一个完整的`Illuminate\Http\Response`实例或视图，返回一个完整的Response实例允许你自定义响应的HTTP状态码和头信息。Responses实例继承自`Symfony\Component\HttpFoundation\Response`类，该类提供了一系列方法用于创建HTTP响应：

```php
use Illuminate\Http\Response;

Route::get('home', function () {
return (new Response($content, $status))
->header('Content-Type', $value);
});
```

为了方便，还可以使用辅助函数`response`:

```php
Route::get('home', function () {
    return response($content, $status)
        ->header('Content-Type', $value);
});
```

_注意：要查看完整的Response方法列表，看可以查看[API文档](https://laravel.com/api/master/Illuminate/Http/Response.html)与[Symfony API 文档](http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/Response.html)_

### 5.5.1.2 添加响应头

大部分响应方法可以用方法链形式调用，从而使得可以建立平滑的响应。

例如，你可以在响应送出给使用者之前，使用`header`方法增加一系列的响应头到响应：

```php
return response($content)
            ->header('Content-Type', $type)
            ->header('X-Header-One', 'Header Value')
            ->header('X-Header-Two', 'Header Value');
```

或者，可以使用`withHeaders`方法指定要增加至回应的响应头数组：

```php
return response($content)
    ->withHeaders([
        'Content-Type' => $type,
        'X-Header-One' => 'Header Value',
        'X-Header-Two' => 'Header Value',
    ]);
```

### 5.5.1.3 添加Cookie到响应

透过响应实例的函数`cookie`可以轻松添加Cookie到响应：

```php
return response($content)->header('Content-Type', $type)
    ->cookie('name', 'value');
```

`cookie`方法接收额外的可选参数，使你可以进一步自定cookies的属性：

```php
->cookie($name, $value, $minutes, $path, $domain, $secure, $httpOnly)
```

默认情况下，laravel框架生成的Cookie经过了加密和签名，以免在客户端被纂改。

如果想要让特定的Cookie子集在生成时取消加密，可以使用中间件`App\Http\Middleware\EncryptCookies`的`$except`属性来排除这些Cookie：

```php
/**
 * 不需要被加密的cookies名称
 *
 * @var array
 */
protected $except = [
    'cookie_name',
];
```

--------------------------------------------------------------------------------

## 5.5.2 其他响应类型

辅助函数`response`可以很方便地用来生成其他类型的响应实例，当无参数调用`response`时会返回`Illuminate\Contracts\Routing\ResponseFactory`的一个实现类实例契约，该契约提供了一些有用的方法来生成响应。

### 5.5.2.1 视图

如果需要控制响应状态和响应头，并还需要返回一个视图作为响应内容，可以使用`view`方法:

```
return response()->view('hello', $data)->header('Content-Type', $type);
```

当然，如果不需要传递自定义的HTTP状态码和头信息，只需要简单使用全局函数`view`即可。

### 5.5.2.2 JSON

`json`方法会自动将`Content-Type`头设置为`application/json`,并使用PHP函数`json_encode`方法将给定数组转化为JSON：

```
return response()->json(['name' => 'Abigail', 'state' => 'CA']);
```

如果要创建一个JSONP响应，可以在`json`方法之后调用`setCallback`方法：

```
return response()->json(['name' => 'Abigail', 'state' => 'CA'])
    ->setCallback($request->input('callback'));
```

### 5.5.2.3 文件下载

`download`方法用于生成强制用户浏览器下载给定路由文件的响应。`download`文件接受文件名作为第二个参数，该参数决定用户下载文件的显示名称，可以将HTTP头信息作为第三个参数传递到该方法：

```
return response()->download($pathToFile);
return response()->download($pathToFile, $name, $headers);
```

_注意：管理文件下载的 Symfony HttpFoundation 类要求被下载文件有一个ASCII文件名。_

--------------------------------------------------------------------------------

## 5.5.3 重定向

重定向响应是`Illuminate\Http\RedirectResponse`类的实例，其中包含了必须的头信息将用户重定向到另一个 URL，有很多方式来生成 `RedirectResponse` 实例，最简单的方法就是使用全局辅助函数 `redirect`：

```php
Route::get('dashboard', function () {
    return redirect('home/dashboard');
});
```

有时候想要将用户重定向到上一个请求的位置。

例如，表单提交后，验证不通过，就可以使用辅助函数`back`返回到前一个URL(使用该方法之前确保路由使用了`web`中间件组或都使用了`session`中间件)：

```php
Route::post('user/profile', function () {
    // 验证请求...
    return back()->withInput();
});
```

### 5.5.3.1 重定向到命名路由

如果调用不带参数的`redirect`方法，会返回一个`Illuminate\Routing\Redirector`实例，然后可以调用该实例上的任何方法。

例如:为了生成一个`RedirectResponse`到命令路由，可以使用`route`方法：

```
return redirect()->route('login');
```

如果路由中有参数，可以将其作为第二个参数传递到`route`方法：

```
// For a route with the following URI: profile/{id}
return redirect()->route('profile', [1]);
```

如果要重定向到带ID参数的路由(Eloquent 模型绑定),可以传递模型本身，ID会被自动解析出来：

```
return redirect()->route('profile', [$user]);
```

### 5.5.3.2 重定向到控制器动作

还可以生成重定向到控制器动作，只需传递控制器和动作名到`action`方法即可。记住，不需要指定控制器的完整命名。laravel的`RouteServiceProvider`会自动设置默认的控制器命名：

```
return redirect()->action('HomeController@index');
```

当然，如果控制器路由要求参数，可以将参数作为第二个参数传递给`action`方法：

```
return redirect()->action('UserController@profile', [1]);
```

### 5.5.3.3 带一次性 Session 数据的重定向

重定向到一个新的URL并将数据存储到一次性Session中通常是同时完成的，为了方便，可以创建一个`RedirectResponse`实例然后在同一方法上将数据存储到Session，这种方式在action之后存储状态信息特别方便：

```php
Route::post('user/profile', function () {
    // 更新用户属性...
    return redirect('dashboard')->with('status', 'Profile updated!');
});
```

当然，用户重定向到新页面之后，你可以从Session中取出并显示这些一次性信息。

例如，使用Blade语法实现：

```
@if (session('status'))
    <div class="alert alert-success">
        {{ session('status') }}
    </div>
@endif
```

--------------------------------------------------------------------------------

## 5.5.4 响应宏

如果想要定义一个自定义的响应并且在多个路由和控制器中复用，可以使用`Illuminate\Contracts\Routing\ResponseFactory`类，或者`Response`函数中的`macro`方法。

例如：在某个服务提供者的boot方法中编写代码如下：

```php
<?php

namespace App\Providers;

use Response;
use Illuminate\Support\ServiceProvider;

class ResponseMacroServiceProvider extends ServiceProvider
{
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        Response::macro('caps', function ($value) {
            return Response::make(strtoupper($value));
        });
    }
}
```

`macro`方法接收响应名称作为第一个参数，闭包函数作为第二个参数，`macro`的闭包在`ResponseFactory`类或`Response`函数中的`macro`方法时执行：

```
return response()->caps('foo');
```
