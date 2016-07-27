## 9. 服务

## 9.1 用户认证

主要可以理解为`Auth`的各种方法。

### 9.1.1 简介

Laravel 中实现用户认证非常简单。实际上，几乎所有东西都已经为你配置好了。配置文件位于`config/auth.php`，其中包含了用于调整认证服务行为的、文档友好的选项配置。

在底层代码中，Laravel 的认证组件由`guards`和`providers`组成，`Guard`定义了用户在每个请求中如何实现认证。

例如，Laravel 通过 `session guard`来维护`Session`存储的状态、`Cookie` 以及 `token guard`，`token guard `是认证用户发送请求时带的`API token`。

`Provider`定义了如何从持久化存储中获取用户信息，Laravel 底层支持通过 Eloquent 和数据库查询构建器两种方式来获取用户，如果需要的话，你还可以定义额外的`Provider`。

如果看到这些名词觉得云里雾里，大可不必太过担心，因为对绝大多数应用而言，只需使用默认认证配置即可，不需要做什么改动。

* **数据库考量**

	默认情况下，Laravel 在`app`目录下包含了一个`Eloquent`模型`App\User`，这个模型可以和默认的 Eloquent 认证驱动一起使用。
	
	如果你的应用不使用 Eloquent，你可以使用`database`认证驱动，该驱动使用了 Laravel 查询构建器。
	
	为 `App\User` 模型构建数据库表结构的时候，确保 `password` 字段长度至少有`60`位。
	
	还有，你应该验证 `users` 表包含了可以为空的、字符串类型的`remember_token`字段长度为`100`，该字段用于存储被应用维护的`记住我（remember me）`的`Session`令牌，这可以通过在迁移中使用 `$table->rememberToken()`; 来实现。


----
	
### 9.1.2 快速入门

Laravel 开箱提供了两个认证控制器，位于 `App\Http\Controllers\Auth` 目录下，`AuthController` 处理新用户注册和登录，`PasswordController` 用于帮助用户找回密码。

每个控制器都使用 `trait` 来引入它们需要的方法。对很多应用而言，你根本不需要修改这两个控制器。

#### 9.1.2.1 路由

Laravel 通过运行如下命令可快速生成认证所需要的路由和视图：

	php artisan make:auth

运行该命令会生成注册和登录视图，以及所有的认证路由，同时生成 `HomeController` ，因为登录成功后会跳转到该控制器下的动作。当然，你也可以不用这个命令根据应用需求完全自定义或者移除这个控制器。

#### 9.1.2.2 视图

正如上面所提到的，`php artisan make:auth` 命令会在 `resources/views/auth` 目录下创建所有认证需要的视图。

`make:auth` 命令还创建了`resources/views/layouts`目录，该目录下包含了应用的基础布局文件。所有这些视图都使用了 `Bootstrap CSS` 框架，你也可以根据需要对其进行自定义。

#### 9.1.2.3 认证

现在你已经为自带的认证控制器设置好了路由和视图，接下来我们来实现新用户注册和登录认证。你可以在浏览器中访问定义好的路由，认证控制器默认已经包含了注册及登录逻辑（通过trait）。

* **自定义路径**

	当一个用户成功进行登录认证后，默认将会跳转到`/`，你可以通过在 `AuthController`中设置`redirectTo`属性来自定义登录认证成功之后的跳转路径：
	
	```php
	protected $redirectTo = '/home';
	```
	当一个用户登录认证失败后，默认将会跳转回登录表单对应的页面。

* **自定义Guard**

	你还可以自定义实现用户认证的`guard`，要实现这一功能，需要在 `AuthController` 中定义 `guard` 属性，该属性的值对应认证配置文件`auth.php`中的相应`guard`配置：
	
	```php
	protected $guard = 'admin';
	```

* **自定义验证/存储**

	要修改新用户注册所必需的表单字段，或者自定义新用户字段如何存储到数据库，你可以修改 `AuthController` 类。该类负责为应用验证输入参数和创建新用户。
	
	`AuthController` 的 `validator` 方法包含了新用户的验证规则，你可以按需要自定义该方法。
	
	`AuthController` 的 `create` 方法负责使用 Eloquent ORM 在数据库中创建新的 `App\User` 记录。当然，你也可以基于自己的需要自定义该方法。

#### 9.1.2.4 获取认证用户

你可以通过 `Auth` 门面访问认证用户：

```php
$user = Auth::user();
```
此外，用户通过认证后，你还可以通过 `Illuminate\Http\Request` 实例访问认证用户：
```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

class ProfileController extends Controller{
    /**
     * 更新用户属性.
     *
     * @param  Request  $request
     * @return Response
     */
    public function updateProfile(Request $request)
    {
        if ($request->user()) {
            // $request->user() 返回认证用户实例...
        }
    }
}
```
* **判断当前用户是否通过认证**

	要判断某个用户是否登录到应用，可以使用 `Auth` 门面的 `check`方法，如果用户通过认证则返回 `true`：
	
	```php
	if (Auth::check()) {
	    // The user is logged in...
	}
	```
此外，你还可以在用户访问特定路由/控制器之前使用中间件来验证用户是否通过认证，想要了解更多，可以查看下面的路由保护。

#### 9.1.2.5 路由保护

路由中间件可用于只允许通过认证的用户访问给定路由。Laravel 通过定义在 `app\Http\Middleware\Authenticate.php` 的 `auth` 中间件来处理这一操作。你所要做的仅仅是将该中间件加到相应的路由定义中：

```php
// 使用路由闭包...
Route::get('profile', ['middleware' => 'auth', function() {
    // 只有认证用户可以进入...
}]);
// 使用控制器...
Route::get('profile', [
    'middleware' => 'auth',
    'uses' => 'ProfileController@show'
]);
```
当然，如果你正在使用控制器类，也可以在控制器的构造方法中调用 `middleware` 方法而不是在路由器中直接定义：

```php
public function __construct(){
    $this->middleware('auth');
}
```
* **指定一个Guard**

	添加 `auth` 中间件到路由后，还需要指定使用哪个 `guard` 来实现认证：
	
	```php
	Route::get('profile', [
	    'middleware' => 'auth:api',
	    'uses' => 'ProfileController@show'
	]);
	```
	指定的 guard 对应配置文件 `auth.php` 中 `guards` 数组的某个键。

#### 9.1.2.6 登录失败次数限制

如果你使用了 Laravel 内置的 `AuthController` 类， 可以使用 `Illuminate\Foundation\Auth\ThrottlesLogins`来限制用户登录失败次数。

默认情况下，用户在几次登录失败后将在一分钟内不能登录，这种限制基于用户的用户名/邮箱地址+IP地址：

```php
<?php

namespace App\Http\Controllers\Auth;

use App\User;use Validator;
use App\Http\Controllers\Controller;
use Illuminate\Foundation\Auth\ThrottlesLogins;
use Illuminate\Foundation\Auth\AuthenticatesAndRegistersUsers;

class AuthController extends Controller{
    use AuthenticatesAndRegistersUsers, ThrottlesLogins;

    // AuthController类的其它部分...
}
```

-----

### 9.1.3 手动认证用户

* **指定额外条件**

* **访问指定 Guard 实例**

* **退出**

#### 9.1.3.1 记住用户

#### 9.1.3.2 其它认证方法

* **认证一个用户实例**

* **通过ID认证用户**

* **一次性认证用户**

------

### 9.1.4 基于HTTP的基本认证

* **FastCGI上注意点**

#### 9.1.4.1 无状态的HTTP基本认证


-----

### 9.1.5 重置密码

#### 9.1.5.1 数据库考量

* **生成重置令牌表迁移**

#### 9.1.5.2 路由

#### 9.1.5.3 视图

#### 9.1.5.4 重置密码后

#### 9.1.5.5 自定义

* **自定义认证 Guard**

* **自定义密码 broker**


----


### 9.1.6 社会化登录认证

#### 9.1.6.1 配置

#### 9.1.6.2 基本使用

* **获取用户信息**

-----

### 9.1.7 添加自定义的Guard


----

### 9.1.8 添加自定义用户提供者

#### 9.1.8.1 UserProvider 契约

#### 9.1.8.2 Authenticatable 契约


----------

### 9.1.9 事件


-----


## 9.2 用户授权

### 9.2.1 简介


----

### 9.2.2 定义权限

* **基于类的权限**

* **拦截认证检查**



-----

### 9.2.3 检查权限

#### 9.2.3.1 通过 Gate 门面

* **为指定用户检查权限**

* **传递多个参数**

#### 9.2.3.2 通过 User 模型

#### 9.2.3.3 在 Blade 模板引擎中检查

#### 9.2.3.4 在表单请求中检查

-------

### 9.2.4 策略类

#### 9.2.4.1 创建策略类

* **注册策略类**

#### 9.2.4.2 编写策略类

* **拦截所有检查**

#### 9.2.4.3 检查策略

* **通过 Gate 门面**

* **通过 User 模型**

* **在 Blade 模板中使用**

* **通过辅助函数 policy**


-----

### 9.2.5 控制器授权

* **自动判断策略类方法**

-----
