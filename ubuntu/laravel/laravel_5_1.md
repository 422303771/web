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

-----


### 5.1.4 路由群组

#### 5.1.4.1 中间件

#### 5.1.4.2 命名空间

#### 5.1.4.3 子域名路由

#### 5.1.4.4 路由前缀


-----


### 5.1.5 CSRF保护

### 5.1.5.1 简介

### 5.1.5.2 不受 CSRF 保护的 URIs

### 5.1.5.3 X-CSRF-Token

### 5.1.5.4 X-XSRF-Token


----



### 5.1.6 路由模型绑定

### 5.1.6.1 隐式绑定

* **自定义键名**

### 5.1.6.2 显式绑定

* **绑定参数到模型**
	
* **自定义解析逻辑**
	
* **自定义"Not Found"**
	
----

### 5.1.7 表单方法伪造

-----