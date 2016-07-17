## 5 基本功能

## 5.1 路由

### 5.1.1 基本路由


laravel所有的路由都可以定义在`App/Http/routes.php`文件，它会被`App/Providers/RouteServiceProvider`类载入。

下方是一个最基本的`get`与`post`。`get`接收URL，`post`为一个闭包。

```php
Route::get('foo', function () {
    return 'Hello World';
});

Route::post('foo', function () {
    //
});
```
默认情况下，`routes.php`文件包含单个路由和一个套用`web`中间件的路由组。

这个中间件路由组提供了`session`状态以及`CSRF`保护。

一般情况下，会将全部路由放置在这个路由组。


#### 5.1.1.1 有效的路由方法

注册路由来响应任何HTTP请求：

下方是HTTP请求方法：

```php
Route::get($uri, $callback);
Route::post($uri, $callback);
Route::put($uri, $callback);
Route::patch($uri, $callback);
Route::delete($uri, $callback);
Route::options($uri, $callback);
```
有时候还需要注册路由响应多个HTTP请求，这时可以使用`match`方法来实现。甚至可以使用`any`方法注册一个路由来响应所有HTTP请求：

```php
Route::match(['get', 'post'], '/', function () {
    //
});

Route::any('foo', function () {
    //
});
```

> 当路由过多时，使用`控制器`是个好方法。


-----


### 5.1.2 路由参数

#### 5.1.2.1 基础路由参数

有时可能需要在路由中捕获URL片段。比如，要从URL中捕获用户ID，需要通过下方代码定义路由参数：
```php
Route::get('user/{id}', function ($id) {
    return 'User '.$id;
});
```
可以按照需要在路由中定义多个路由参数：

```php
Route::get('posts/{post}/comments/{comment}', function ($postId, $commentId) {
    //
});
```
路由参数总是通过`{ }`进行包裹，这些参数在路由被执行时会被传递到路由的闭包。

*注意：路由参数不能包含`-`,要使用`_`替换。*

#### 5.1.2.2 选择性路由参数

有时候可能需要指定可选的路由参数，这可以通过在参数后加`?`标记来实现，这种情况下需要指定变量的默认值：

下方代码先设定`name可以为空`，再设定默认`name为John`。 

```php
Route::get('user/{name?}', function ($name = null) {
    return $name;
});

Route::get('user/{name?}', function ($name = 'John') {
    return $name;
});
```


#### 5.1.2.3 正则约束

路由设置中可以使用`where`方法来约束路由参数。`where`方法接收参数名和一个正则表达式来定义参数如何被约束：

```php
Route::get('user/{name}', function ($name) {
    //
})->where('name', '[A-Za-z]+');

Route::get('user/{id}', function ($id) {
    //
})->where('id', '[0-9]+');

Route::get('user/{id}/{name}', function ($id, $name) {
    //
})->where(['id' => '[0-9]+', 'name' => '[a-z]+']);
```

#### 5.1.2.4 全局约束

如果想要全局约束，可以使用`pattern`方法。在`RouteServiceProvider`类的`boot`方式中定义约束：

```php
/**
 * 定义路由模型绑定，模式过滤器等
 *
 * @param  \Illuminate\Routing\Router  $router
 * @return void
 * @translator  http://laravelacademy.org
 */
public function boot(Router $router){
    $router->pattern('id', '[0-9]+');
    parent::boot($router);
}
```
一旦模式被定义，将会自动应用到所有包含参数名的路由中：

```php
Route::get('user/{id}', function ($id) {
    // 只有当 {id} 是数字时才会被调用
});
```

上方例子中限定`id`只能是数字。

-----


### 5.1.3 命令路由

命令路由为生成URL或重新定向提供方便。在定义路由是使用`as`指定路由名称：

```php
Route::get('user/profile', ['as' => 'profile', function () {
    //
}]);
```
此外，还可以为控制器动作指定路由名称：

```php
Route::get('user/profile', [
    'as' => 'profile', 'uses' => 'UserController@showProfile'
]);
```
还有另一种，路由命名的方法，比如使用`name`方式来实现：

```php
Route::get('user/profile',function () {
	//
})->name('profile');
```
![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/laravel/img/laravel_1.png)

#### 5.1.3.1 路由群组 & 命名路由

如果你在使用路由群组，可以通过在路由群组的属性数组中指定`as`关键字来为群组的路由设置一个共用的路由名前缀：

```php
Route::group(['as' => 'admin::'], function () {
    Route::get('dashboard', ['as' => 'dashboard', function () {
        // 路由被命名为 "admin::dashboard"
    }]);
});
```
![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/laravel/img/laravel_2.png)

#### 5.1.3.2 为命名路由生成URL

如果为给定路由进行了命名，就可以通过全局`route`函数为该命名路由生成对应URL，或者重新定向：



```php
$url = route('profile');		//产生URL
$redirect = redirect()->route('profile');		//重新定向
```
如果命名路由定义了参数，可以将该参数作为第二个参数穿传递给`route`函数。路由参数会自动插入URL中。

```php
Route::get('user/{id}/profile', ['as' => 'profile', function ($id) {
     //
}]);
$url = route('profile', ['id' => 1]);
```

-----


### 5.1.4 路由群组

路由组允许共享路由属性，比如中间件与命名空间等。这样利用路由群组套用这些属性到多个路由，而不需要在每个路由都设定一次。

共享属性被要求为数组格式。共享属性作为第一个参数被传递给`Route::group`方法。

#### 5.1.4.1 中间件

要给路由组中所有路由分配中间件，可以在群组属性中使用`middleware`。中间件将会按照数组中定义的顺序依次执行：

```php
Route::group(['middleware' => 'auth'], function () {
    Route::get('/', function () {
        // 使用 Auth 中间件
    });

    Route::get('user/profile', function () {
        // 使用 Auth 中间件
    });
});
```

#### 5.1.4.2 命名空间

另一个例子，指定相同的PHP命名空间下多个控制器，可以在分组属性数组中使用`namespace`来指定群组中所有控制器的公共命名空间：

```php
Route::group(['namespace' => 'Admin'], function(){
    // 控制器在 "App\Http\Controllers\Admin" 命名空间下

    Route::group(['namespace' => 'User'], function(){
        // 控制器在 "App\Http\Controllers\Admin\User" 命名空间下
    });
});
```
默认情况下，`RouteServiceProvider`会在命名空间群组内导入`routes.php`文件，让你不用指定完整`App/Http/Controllers`命令空间，就能注册控制器。所有，只需要指定在`App/Http/Controllers`之后的命名。

#### 5.1.4.3 子域名路由

子域名可以像URL一样被分配给路由参数，从而允许捕获子域名的部分用于路由或者控制器，子域名可以通过群组属性中的`domani`来指定：

```php
Route::group(['domain' => '{account}.myapp.com'], function () {
    Route::get('user/{id}', function ($account, $id) {
        //
    });
});
```
*注意：其中的{account}可以随意修改的。{id}也是可以随意更改的。*


#### 5.1.4.4 路由前缀

`prefix`群组属性，用来为群组中每个路由添加一个给定URL前缀。

例如，你可以为所有路由URL添加`admin`前缀：
```php
Route::group(['prefix' => 'admin'], function () {
    Route::get('users', function () {
        // 匹配 "/admin/users" URL
    });
});
```
还可以使用`prefix`参数为路由组指定公共路由参数：

```php
Route::group(['prefix' => 'accounts/{account_id}'], function () {
    Route::get('detail', function ($account_id) {
        // 匹配 accounts/{account_id}/detail URL
    });
});
```

-----


### 5.1.5 CSRF保护

#### 5.1.5.1 简介

CSRF指跨网站请求伪造。跨网站请求伪伪造是一种恶意攻击，透过经身份验证的使用者身份执行未经授权的命令。

laravel会自动产生一个CSRF令牌给每个被应用管理的有效的用户，该令牌用于验证授权用户和发起请求者是否为同一个人。

想要生成包含CSRF令牌的隐藏输入字段，可以使用`csrf_field`函数来实现：
```php
<?php echo csrf_field(); ?>
```
`csrf_field`函数会生成如下HTML：

```html
<input type="hidden" name="_token" value="<?php echo csrf_token(); ?>">
```
当然推荐使用`Blade`引擎模板提供的方式：
```
{!! csrf_field() !!}
```
不需要自己编写代码去验证POST、PUT或者DELETE请求的CSRF令牌，因为Laravel自带的HTTP中间件`VerfyCsrfToken`会完成这些工作。

只要将请求中的输入`token`和Session中存储的`token`作对比来进行验证。

### 5.1.5.2 不受 CSRF 保护的 URIs

有时需要从CSRF保护中排除一些URL。比如，如果使用`Stripe`来处理支付并用到他们的webhook系统，这时就需要从laravel的CSRF保护中排除webhook处理路由。

要实现这一目的，需要在`VerifyCsrfToken`中间件中将要排除的URL添加到`$except`属性：

```php
<?php

namespace App\Http\Middleware;

use Illuminate\Foundation\Http\Middleware\VerifyCsrfToken as BaseVerifier;

class VerifyCsrfToken extends BaseVerifier
{
    /**
     *从CSRF验证中排除的URL
     *
     * @var array
     */
    protected $except = [
        'stripe/*',
    ];
}
```

#### 5.1.5.3 X-CSRF-Token

除了检查当前POST参数的CDSRF令牌外，在laravel的`VerifyCsrfToken`中介层也会确认请求头部中的`X-CSRF-Token`。

例如，可以将其存储在meta标签中：

```html
<meta name="csrf-token" content="{{ csrf_token() }}">
```
一旦建立了`meta`标签，就可以使用jQuery之类的函数库，将`令牌`加入到所有请求头部。

这为基于AJAX的应用提供了简单、方便的方式来避免CSRF攻击。

```js
$.ajaxSetup({
    headers: {
        'X-CSRF-TOKEN': $('meta[name="csrf-token"]').attr('content')
    }
});
```

#### 5.1.5.4 X-XSRF-Token

laravel还会将CSRF令牌保存到名为`XSRF-TOKEN`的Cookie中，可以使用该Cookie值来设置`X-XSRF-TOKEN`请求头。

一些javascript框架，比如Angular，会为你自动进行设置，基本上不太需要手动设置这个值。

----



### 5.1.6 路由模型绑定

laravel路由模型绑定提供了一个方便的方式来注入类至路由中。

例如，可以将匹配到`ID`的整个User类注入到路由中，而不是直接注入用户ID。

#### 5.1.6.1 隐式绑定

laravel会自动解析定义在路由或控制器动作（变量名匹配路由片段）中的Eloquent模型类型声明，例如：

```php
Route::get('api/users/{user}', function (App\User $user) {
    return $user->email;
});
```

这个例子中，路由URL的`user`符合`$user`实例的ELoquent模型，所以laravel会自动注入与请求URL的ID对应的模型实例。

如果找不到对应的模型实例，会自动生成HTTP404响应。

* **自定义键名**

	如果想要隐式模型绑定，使用数据表的其他字段，可以重写Eloquent模型类的`getRouteKeyName`方法：
	
	```php
	/**
	 * Get the route key for the model.
	 *
	 * @return string
	 */
	public function getRouteKeyName()
	{
	    return 'slug';
	}
	```
	现在就可以使用`slug`作为键值了，可以用来`模糊关键字`。

#### 5.1.6.2 显式绑定

要注册显式绑定，需要使用路由的`model`方法来为给定参数指定绑定类。

必须在`RouteServiceProvider::boot`方法中定义模型绑定。

* **绑定参数到模型**
	
	```php
	public function boot(Router $router)
	{
	    parent::boot($router);
	    $router->model('user', 'App\User');
	}
	```
	接下来，定义一个包含`user`参数的路由：
	
	```php
	$router->get('profile/{user}', function(App\User $user) {
	     //
	});
	```
	由于已经绑定`{user}`参数到`App\User`模型，User实例会被注入到该路由。因此，如果请求URL是`profile/1`,就会注入一个用户ID为1的User实例。
	
	如果匹配的模型实例在数据库不存在，会自动生成并返回HTTP404响应。

* **自定义解析逻辑**	
	
	如果想要使用自定义的解析逻辑需要使用`Route::bind`方法，传递到`bind`方法的闭关会获取到URL请求的参数中的值，并返回你想要在该路由中注入的类实例：
	
	```php
	$router->bind('user', function($value) {
	    return App\User::where('name', $value)->first();
	});
	```
* **自定义"Not Found"**
	
	如果想要指定自己的`Not Found`行为，将封装该行为的闭包作为第三个参数传递给`model`方法
	
	```php
	$router->model('user', 'App\User', function() {
	    throw new NotFoundHttpException;
	});
	```

----

### 5.1.7 表单方法伪造

HTML表单不支持PUT、PATCH或者DELETE请求方法。当使用这些路由时，需要添加一个隐藏的`_method`字段到表单中，其值被用作该表单的HTTP请求方法：

```html
<form action="/foo/bar" method="POST">
    <input type="hidden" name="_method" value="PUT">
    <input type="hidden" name="_token" value="{{ csrf_token() }}">
</form>
```
还可以使用辅助函数`method_field`来实现这一目的：

```php
<?php echo method_field('PUT'); ?>
```
当然，也支持Blade模板引擎：

	{{ method——field('PUT') }}

-----