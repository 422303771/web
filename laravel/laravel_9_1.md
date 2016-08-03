<!--toc-->

- [9\. 服务](#9-服务)
- [9.1 用户认证](#91-用户认证)
	- [9.1.1 简介](#911-简介)
	- [9.1.2 快速入门](#912-快速入门)
		- [9.1.2.1 路由](#9121-路由)
		- [9.1.2.2 视图](#9122-视图)
		- [9.1.2.3 认证](#9123-认证)
		- [9.1.2.4 获取认证用户](#9124-获取认证用户)
		- [9.1.2.5 路由保护](#9125-路由保护)
		- [9.1.2.6 登录失败次数限制](#9126-登录失败次数限制)
	- [9.1.3 手动认证用户](#913-手动认证用户)
		- [9.1.3.1 记住用户](#9131-记住用户)
		- [9.1.3.2 其它认证方法](#9132-其它认证方法)
	- [9.1.4 基于HTTP的基本认证](#914-基于http的基本认证)
		- [9.1.4.1 无状态的HTTP基本认证](#9141-无状态的http基本认证)
	- [9.1.5 重置密码](#915-重置密码)
		- [9.1.5.1 数据库考量](#9151-数据库考量)
		- [9.1.5.2 路由](#9152-路由)
		- [9.1.5.3 视图](#9153-视图)
		- [9.1.5.4 重置密码后](#9154-重置密码后)
		- [9.1.5.5 自定义](#9155-自定义)
	- [9.1.6 社会化登录认证](#916-社会化登录认证)
		- [9.1.6.1 配置](#9161-配置)
		- [9.1.6.2 基本使用](#9162-基本使用)
	- [9.1.7 添加自定义的Guard](#917-添加自定义的guard)
	- [9.1.8 添加自定义用户提供者](#918-添加自定义用户提供者)
		- [9.1.8.1 UserProvider 契约](#9181-userprovider-契约)
		- [9.1.8.2 Authenticatable 契约](#9182-authenticatable-契约)
	- [9.1.9 事件](#919-事件)
- [9.2 用户授权](#92-用户授权)
	- [9.2.1 简介](#921-简介)
	- [9.2.2 定义权限（Abilities）](#922-定义权限abilities)
	- [9.2.3 检查权限](#923-检查权限)
		- [9.2.3.1 通过 Gate 门面](#9231-通过-gate-门面)
		- [9.2.3.2 通过 User 模型](#9232-通过-user-模型)
		- [9.2.3.3 在 Blade 模板引擎中检查](#9233-在-blade-模板引擎中检查)
		- [9.2.3.4 在表单请求中检查](#9234-在表单请求中检查)
	- [9.2.4 策略类](#924-策略类)
		- [9.2.4.1 创建策略类](#9241-创建策略类)
		- [9.2.4.2 编写策略类](#9242-编写策略类)
		- [9.2.4.3 检查策略](#9243-检查策略)
	- [9.2.5 控制器授权](#925-控制器授权)

<!-- tocstop -->

----

# 9\. 服务

# 9.1 用户认证

主要可以理解为`Auth`的各种方法。

## 9.1.1 简介

Laravel 中实现用户认证非常简单。实际上，几乎所有东西都已经为你配置好了。配置文件位于`config/auth.php`，其中包含了用于调整认证服务行为的、文档友好的选项配置。

在底层代码中，Laravel 的认证组件由`guards`和`providers`组成，`Guard`定义了用户在每个请求中如何实现认证。

例如，Laravel 通过 `session guard`来维护`Session`存储的状态、`Cookie` 以及 `token guard`，`token guard`是认证用户发送请求时带的`API token`。

`Provider`定义了如何从持久化存储中获取用户信息，Laravel 底层支持通过 Eloquent 和数据库查询构建器两种方式来获取用户，如果需要的话，你还可以定义额外的`Provider`。

如果看到这些名词觉得云里雾里，大可不必太过担心，因为对绝大多数应用而言，只需使用默认认证配置即可，不需要做什么改动。

- **数据库考量**

  默认情况下，Laravel 在`app`目录下包含了一个`Eloquent`模型`App\User`，这个模型可以和默认的 Eloquent 认证驱动一起使用。

  如果你的应用不使用 Eloquent，你可以使用`database`认证驱动，该驱动使用了 Laravel 查询构建器。

  为 `App\User` 模型构建数据库表结构的时候，确保 `password` 字段长度至少有`60`位。

  还有，你应该验证 `users` 表包含了可以为空的、字符串类型的`remember_token`字段长度为`100`，该字段用于存储被应用维护的`记住我（remember me）`的`Session`令牌，这可以通过在迁移中使用 `$table->rememberToken()`; 来实现。

--------------------------------------------------------------------------------

## 9.1.2 快速入门

Laravel 开箱提供了两个认证控制器，位于 `App\Http\Controllers\Auth` 目录下，`AuthController` 处理新用户注册和登录，`PasswordController` 用于帮助用户找回密码。

每个控制器都使用 `trait` 来引入它们需要的方法。对很多应用而言，你根本不需要修改这两个控制器。

### 9.1.2.1 路由

Laravel 通过运行如下命令可快速生成认证所需要的路由和视图：

```
php artisan make:auth
```

运行该命令会生成注册和登录视图，以及所有的认证路由，同时生成 `HomeController` ，因为登录成功后会跳转到该控制器下的动作。当然，你也可以不用这个命令根据应用需求完全自定义或者移除这个控制器。

### 9.1.2.2 视图

正如上面所提到的，`php artisan make:auth` 命令会在 `resources/views/auth` 目录下创建所有认证需要的视图。

`make:auth` 命令还创建了`resources/views/layouts`目录，该目录下包含了应用的基础布局文件。所有这些视图都使用了 `Bootstrap CSS` 框架，你也可以根据需要对其进行自定义。

### 9.1.2.3 认证

现在你已经为自带的认证控制器设置好了路由和视图，接下来我们来实现新用户注册和登录认证。你可以在浏览器中访问定义好的路由，认证控制器默认已经包含了注册及登录逻辑（通过trait）。

- **自定义路径**

  当一个用户成功进行登录认证后，默认将会跳转到`/`，你可以通过在 `AuthController`中设置`redirectTo`属性来自定义登录认证成功之后的跳转路径：

  ```php
    protected $redirectTo = '/home';
  ```

  当一个用户登录认证失败后，默认将会跳转回登录表单对应的页面。

- **自定义Guard**

  你还可以自定义实现用户认证的`guard`，要实现这一功能，需要在 `AuthController` 中定义 `guard` 属性，该属性的值对应认证配置文件`auth.php`中的相应`guard`配置：

  ```php
    protected $guard = 'admin';
  ```

- **自定义验证/存储**

  要修改新用户注册所必需的表单字段，或者自定义新用户字段如何存储到数据库，你可以修改 `AuthController` 类。该类负责为应用验证输入参数和创建新用户。

  `AuthController` 的 `validator` 方法包含了新用户的验证规则，你可以按需要自定义该方法。

  `AuthController` 的 `create` 方法负责使用 Eloquent ORM 在数据库中创建新的 `App\User` 记录。当然，你也可以基于自己的需要自定义该方法。

### 9.1.2.4 获取认证用户

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

- **判断当前用户是否通过认证**

  要判断某个用户是否登录到应用，可以使用 `Auth` 门面的 `check`方法，如果用户通过认证则返回 `true`：

  ```php
    if (Auth::check()) {
        // The user is logged in...
    }
  ```

  此外，你还可以在用户访问特定路由/控制器之前使用中间件来验证用户是否通过认证，想要了解更多，可以查看下面的路由保护。

### 9.1.2.5 路由保护

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

- **指定一个Guard**

  添加 `auth` 中间件到路由后，还需要指定使用哪个 `guard` 来实现认证：

  ```php
    Route::get('profile', [
        'middleware' => 'auth:api',
        'uses' => 'ProfileController@show'
    ]);
  ```

  指定的 guard 对应配置文件 `auth.php` 中 `guards` 数组的某个键。

### 9.1.2.6 登录失败次数限制

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

--------------------------------------------------------------------------------

## 9.1.3 手动认证用户

当然，你也可以不使用 Laravel 自带的认证控制器。

如果你选择移除这些控制器，你需要直接使用 Laravel 认证类来管理用户认证。别担心，这很简单！

我们将会通过 `Auth` 门面来访问认证服务，因此我们需要确保在类的顶部导入了 `Auth` 门面，让我们看看 `attempt` 方法：

```php
<?php

namespace App\Http\Controllers;

use Auth;
use Illuminate\Routing\Controller;

class AuthController extends Controller{
    /**
     * 处理登录认证
     *
     * @return Response
     */
    public function authenticate()
    {
        if (Auth::attempt(['email' => $email, 'password' => $password])) {
            // 认证通过...
            return redirect()->intended('dashboard');
        }
    }
}
```

`attempt`方法接收键值数组对作为第一个参数，数组中的值被用于从数据表中查找用户，因此，在上面的例子中，用户将会通过`email`的值获取，如果用户被找到，经哈希运算后存储在数据中的密码将会和传递过来的经哈希运算处理的密码值进行比较。

如果两个经哈希运算的密码相匹配那么将会为这个用户开启一个认证`Session`。

如果认证成功的话`attempt`方法将会返回`true`。否则，返回`false`。

重定向器上的`intended`方法将用户重定向到登录之前用户想要访问的`URL`，在目标`URL`无效的情况下备用`URI`将会传递给该方法。

- **指定额外条件**

  如果需要的话，除了用户邮件和密码之外还可以在认证查询时添加额外的条件。

  例如，我们可以验证被标记为有效的用户：

  ```php
    if (Auth::attempt(['email' => $email, 'password' => $password, 'active' => 1])) {
    // The user is active, not suspended, and exists.
    }
  ```

  _注意：在这些例子中，并不仅仅限于使用`email`进行登录认证，这里只是作为演示示例，你可以将其修改为数据库中任何其他可用作`username`的字段。_

- **访问指定 Guard 实例**

  你可以使用`Auth`门面的`guard`方法指定想要使用的`guard`实例，这种机制允许你在同一个应用中对不同的认证模型或用户表实现完全独立的用户认证。

  传递给`guard`方法的`guard`名称对应配置文件`auth.php`中`guards`配置的某个键：

  ```php
    if (Auth::guard('admin')->attempt($credentials)) {
    //
    }
  ```

- **退出**

  要退出应用，可以使用`Auth`门面的`logout`方法，这将会清除用户`Session`中的认证信息：

  ```php
    Auth::logout();
  ```

### 9.1.3.1 记住用户

如果你想要在应用中提供`记住我`的功能，可以传递一个布尔值作为第二个参数到`attempt`方法，这样用户登录认证状态就会一直保持直到他们手动退出。

当然，你的`users`表必须包含`remember_token`字段，该字段用于存储`记住我`令牌。

```php
if (Auth::attempt(['email' => $email, 'password' => $password], $remember)) {
    // The user is being remembered...
}
```

如果你要`记住`用户，可以使用`viaRemember`方法来判断用户是否使用`记住我`cookie进行认证：

```php
if (Auth::viaRemember()) {
    //
}
```

### 9.1.3.2 其它认证方法

- **认证一个用户实例**

  如果你需要将一个已存在的用户实例登录到应用中，可以调用`Auth`门面的`login`方法并传入用户实例，传入实例必须是`Illuminate\Contracts\Auth\Authenticatable`契约的实现，当然，Laravel 自带的 `App\User` 模型已经实现了该接口：

  ```php
    Auth::login($user);
  ```

- **通过ID认证用户**

  要通过用户ID登录到应用，可以使用`loginUsingId`方法，该方法接收你想要认证用户的主键作为参数：

  ```php
    Auth::loginUsingId(1);
  ```

- **一次性认证用户**

  你可以使用`once`方法只在单个请求中,将用户登录到应用，而不存储任何`Session`和`Cookie`，这在构建无状态的`API`时很有用。`once`方法和`attempt`方法用法差不多：

  ```php
    if (Auth::once($credentials)) {
        //
    }
  ```

--------------------------------------------------------------------------------

## 9.1.4 基于HTTP的基本认证

HTTP基本认证能够帮助用户快速实现登录认证,而不用设置专门的登录页面，首先要在路由中加上 `auth.basic` 中间件。该中间件是 Laravel 自带的，所以不需要自己定义：

```php
Route::get('profile', ['middleware' => 'auth.basic', function() {
    // 只有认证用户可以进入...
}]);
```

中间件加到路由中后，当在浏览器中访问该路由时，会自动提示需要认证信息，默认情况下，`auth.basic` 中间件使用用户记录上的`email`字段作为`用户名`。

- **FastCGI上注意点**

  如果你使用`PHP FastCGI`，HTTP基本认证将不能正常工作，需要在 `.htaccess`文件加入如下内容：

  ```
    RewriteCond %{HTTP:Authorization} ^(.+)$
    RewriteRule .* - [E=HTTP_AUTHORIZATION:%{HTTP:Authorization}]
  ```

### 9.1.4.1 无状态的HTTP基本认证

使用 HTTP 基本认证也不需要在`Session`中设置用户标识`Cookie`，这在 API 认证中非常有用。要实现这个，需要定义一个调用`onceBasic`方法的中间件。

如果该方法没有返回任何响应，那么请求会继续走下去：

```php
<?php

namespace Illuminate\Auth\Middleware;

use Auth;
use Closure;

class AuthenticateOnceWithBasicAuth{
    /**
     * 处理输入请求.
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  \Closure  $next
     * @return mixed
     */
    public function handle($request, Closure $next)
    {
        return Auth::onceBasic() ?: $next($request);
    }

}
```

接下来，注册路由中间件并将其添加到路由中：

```php
Route::get('api/user', ['middleware' => 'auth.basic.once', function() {
    // 只有认证用户可以进入...
}]);
```

--------------------------------------------------------------------------------

## 9.1.5 重置密码

### 9.1.5.1 数据库考量

大多数 web 应用提供了用户重置密码的功能，Laravel 提供了便利方法用于发送密码提示及执行密码重置而不需要你在每个应用中重新实现。

开始之前，先验证 `App\User` 模型实现了 `Illuminate\Contracts\Auth\CanResetPassword` 契约。当然，Laravel 自带的`App\User`模型已经实现了该接口，并使用 `Illuminate\Auth\Passwords\CanResetPassword`来包含实现该接口需要的方法。

- **生成重置令牌表迁移**

  接下来，用来存储密码重置令牌的表必须被创建，Laravel 已经自带了这张表的迁移，就存放在 `database/migrations` 目录。所有，你所要做的仅仅是运行迁移：

  ```
    php artisan migrate
  ```

### 9.1.5.2 路由

Laravel 自带了`Auth\PasswordController`，其中包含了重置用户密码的相应逻辑。重置密码所需的路由都已经通过`make:auth`命令自动生成了：

```
php artisan make:auth
```

### 9.1.5.3 视图

和路由一样，重置密码所需的视图文件也通过`make:auth`命令一并生成了，这些视图文件位于 `resources/views/auth/passwords`目录下，你可以按照所需对生成的文件进行相应修改。

### 9.1.5.4 重置密码后

定义好重置用户密码路由和视图后，只需要在浏览器中访问这些路由即可。框架自带的 `PasswordController` 已经包含了发送密码重置链接邮件以及更新数据库中密码的逻辑。

密码被重置后，用户将会自动登录到应用并重定向到 `/home`。你可以通过定义上 `PasswordController`的`redirectTo`属性来自定义密码重置成功后的跳转链接：

```
protected $redirectTo = '/dashboard';
```

_注意：默认情况下，密码重置令牌一小时内有效，你可以通过修改`config/auth.php`文件中的选项`reminder.expire` 来改变有效时间。_

### 9.1.5.5 自定义

- **自定义认证 Guard**

  在配置文件`auth.php`中，可以配置多个`guards`，以便用于实现多用户表独立认证，你可以通过添加 `$guard`属性到自带的`PasswordController`控制器的方法来使用你选择的`guard：

  ```
    /**
    * The authentication guard that should be used.
    *
    * @var string
    */
    protected $guard = 'admins';
  ```

- **自定义密码 broker**

  在配置文件`auth.php`中，可以配置多个密码，以便用于重置多个用户表的密码`broker`，同样，可以通过在自带的`PasswordController`控制器中添加`$broker`属性来使用你选择的`broker`：

  ```
    /**
    * The password broker that should be used.
    *
    * @var string
    */
    protected $broker = 'admins';
  ```

--------------------------------------------------------------------------------

## 9.1.6 社会化登录认证

Laravel 中还可以使用`Laravel Socialite`通过`OAuth`提供者进行简单、方便的认证，也就是社会化登录。

目前支持使用Facebook、Twitter、LinkedIn、GitHub和Bitbucket进行登录认证。

要使用社会化登录，需要在`composer.json`文件中添加依赖：

```
composer require laravel/socialite
```

### 9.1.6.1 配置

安装完社会化登录库后，在配置文件`config/app`中注册`Laravel\Socialite\SocialiteServiceProvider`：

```
'providers' => [
    // 其它服务提供者...
    Laravel\Socialite\SocialiteServiceProvider::class,
],
```

还要在`app`配置文件中添加`Socialite`门面到`aliases`数组：

```
'Socialite' => Laravel\Socialite\Facades\Socialite::class,
```

你还需要为应用使用的`OAuth`服务添加认证信息，这些认证信息位于配置文件 `config/services.php`，而且键为`facebook`, `twitter`,`linkedin`, `google`, `github`或`bitbucket`，这取决于应用需要的提供者。

例如：

```
'github' => [
    'client_id' => 'your-github-app-id',
    'client_secret' => 'your-github-app-secret',
    'redirect' => 'http://your-callback-url',
],
```

### 9.1.6.2 基本使用

接下来，准备好认证用户！你需要两个路由：一个用于重定向用户到`OAuth`提供者，另一个用户获取认证后来自提供者的回调。我们使用`Socialite`门面访问`Socialite`：

```php
<?php

namespace App\Http\Controllers;

use Socialite;
use Illuminate\Routing\Controller;

class AuthController extends Controller{
    /**
     * 将用户重定向到GitHub认证页面.
     *
     * @return Response
     */
    public function redirectToProvider()
    {
        return Socialite::driver('github')->redirect();
    }

    /**
     * 从GitHub获取用户信息.
     *
     * @return Response
     */
    public function handleProviderCallback()
    {
        $user = Socialite::driver('github')->user();

        // $user->token;
    }
}
```

`redirect`方法将用户发送到`OAuth`提供者，`user`方法读取请求信息并从提供者中获取用户信息，在重定向用户之前，你还可以在请求上使用`scope`方法设置`作用域`，该方法将会重写已存在的所有作用域：

```
return Socialite::driver('github')
            ->scopes(['scope1', 'scope2'])->redirect();
```

当然，你需要定义路由到控制器方法：

```php
 Route::get('auth/github', 'Auth\AuthController@redirectToProvider');
 Route::get('auth/github/callback', 'Auth\AuthController@handleProviderCallback');
```

- **获取用户信息**

  有了用户实例之后，可以获取用户的更多详情：

  ```php
    $user = Socialite::driver('github')->user();
    // OAuth Two Providers
    $token = $user->token;
    // OAuth One Providers
    $token = $user->token;
    $tokenSecret = $user->tokenSecret;
    // All Providers
    $user->getId();
    $user->getNickname();
    $user->getName();
    $user->getEmail();
    $user->getAvatar();
  ```

--------------------------------------------------------------------------------

## 9.1.7 添加自定义的Guard

你可以通过`Auth`门面的`extend`方法定义自己的认证`guard`，该功能需要在某个服务提供者的`boot` 方法中实现：

```php
<?php

namespace App\Providers;

use Auth;
use App\Services\Auth\JwtGuard;
use Illuminate\Support\ServiceProvider;

class AuthServiceProvider extends ServiceProvider{
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        Auth::extend('jwt', function($app, $name, array $config) {
           // Return an instance of Illuminate\Contracts\Auth\Guard...

            return new JwtGuard(Auth::createUserProvider($config['provider']));
        });
    }

    /**
     * Register bindings in the container.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

正如你在上述例子中所看到的，传递给`extend`方法的闭包回调需要返回 `Illuminate\Contracts\Auth\Guard`的实现实例，该接口包含了自定义认证`guard`需要的一些方法。

定义好自己的认证`guard`之后，可以在配置文件的`guards`配置中使用话这个`guard`：

```
'guards' => [
    'api' => [
        'driver' => 'jwt',
        'provider' => 'users',
    ],
],
```

--------------------------------------------------------------------------------

## 9.1.8 添加自定义用户提供者

如果你没有使用传统的关系型数据库存储用户信息，则需要使用自己的认证用户提供者来扩展 Laravel。

我们使用`Auth`门面上的`provider`方法定义自定义该提供者，在服务提供者调用`provider`方法如下：

```php
<?php

namespace App\Providers;

use Auth;
use App\Extensions\RiakUserProvider;
use Illuminate\Support\ServiceProvider;

class AuthServiceProvider extends ServiceProvider{
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        Auth::provider('riak', function($app,array $config) {
            // 返回Illuminate\Contracts\Auth\UserProvider实例...
            return new RiakUserProvider($app['riak.connection']);
        });
    }

    /**
     * 在容器中注册绑定.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

通过`provider`方法注册用户提供者后，你可以在配置文件`config/auth.php`中切换到新的用户提供者。首先，在该配置文件定义一个使用新驱动的`providers`数组：

```
'providers' => [
    'users' => [
        'driver' => 'riak',
    ],
],
```

然后，可以在你的`guards`配置中使用这个提供者：

```
'guards' => [
    'web' => [
        'driver' => 'session',
        'provider' => 'users',
    ],
],
```

### 9.1.8.1 UserProvider 契约

`Illuminate\Contracts\Auth\UserProvider`实现方法只负责从持久化存储系统中获取`Illuminate\Contracts\Auth\Authenticatable`实现方法，例如`MySQ`L、`Riak`等等。

这两个接口允许 Laravel 认证机制继续起作用而不管用户数据如何存储或者何种类来展现。

让我们先看看`Illuminate\Contracts\Auth\UserProvider`契约：

```php
<?php

namespace Illuminate\Contracts\Auth;

interface UserProvider {

    public function retrieveById($identifier);
    public function retrieveByToken($identifier, $token);
    public function updateRememberToken(Authenticatable $user, $token);
    public function retrieveByCredentials(array $credentials);
    public function validateCredentials(Authenticatable $user, array $credentials);

}
```

`retrieveById`方法通常获取一个代表用户的键，例如 MySQL 数据中的自增ID。该方法获取并返回匹配该ID的`Authenticatabl`实现方法。

`retrieveByToken`函数通过唯一标识和存储在`remember_token`字段中的`记住我`令牌获取用户。和上一个方法一样，该方法也返回`Authenticatabl`实现方法。

`updateRememberToken`方法使用新的`$token`更新`$user`的`remember_token`字段，新令牌可以是新生成的令牌（在登录是选择"记住我"被成功赋值）或者null（用户退出）。

`retrieveByCredentials` 方法在尝试登录系统时获取传递给 `Auth::attempt` 方法的认证信息数组。该方法接下来去底层持久化存储系统查询与认证信息匹配的用户，通常，该方法运行一个带`where`条件（`$credentials[‘username’]`）的查询。然后该方法返回`UserInterface`的实现。这个方法不做任何密码校验和认证。

`validateCredentials`方法比较给定`$user`和`$credentials`来认证用户。例如，这个方法比较 `$user->getAuthPassword()`字符串和经`Hash::make`处理的`$credentials['password']`。这个方法只验证用户认证信息并返回布尔值。

### 9.1.8.2 Authenticatable 契约

既然我们已经探索了`UserProvider`上的每一个方法，接下来让我们看看`Authenticatable`。该提供者应该从`retrieveById`和`retrieveByCredentials`方法中返回接口实现：

```php
<?php

namespace Illuminate\Contracts\Auth;

interface Authenticatable {

    public function getAuthIdentifier();
    public function getAuthPassword();
    public function getRememberToken();
    public function setRememberToken($value);
    public function getRememberTokenName();

}
```

这个接口很简单，`getAuthIdentifier`方法返回用户`主键`，在 MySQL 后台中是ID，`getAuthPassword`返回经哈希处理的用户密码，这个接口允许认证系统处理任何用户类，不管是你使用的是 `ORM` 还是`存储抽象层`。默认情况下，Laravel 自带的`app`目录下的`User`类实现了这个接口，所以你可以将这个类作为实现方法例子。

--------------------------------------------------------------------------------

## 9.1.9 事件

Laravel 支持在认证过程中触发多种事件，你可以在自己的`EventServiceProvider`中监听这些事件：

```php
/**
 * The event listener mappings for the application.
 *
 * @var array
 */
protected $listen = [
    'Illuminate\Auth\Events\Attempting' => [
        'App\Listeners\LogAuthenticationAttempt',
    ],

    'Illuminate\Auth\Events\Login' => [
        'App\Listeners\LogSuccessfulLogin',
    ],

    'Illuminate\Auth\Events\Logout' => [
        'App\Listeners\LogSuccessfulLogout',
    ],
];
```

--------------------------------------------------------------------------------

# 9.2 用户授权

## 9.2.1 简介

除了提供开箱即用的认证服务之外，Laravel 还提供了一个简单的方式来管理授权逻辑以便控制对资源的访问权限。在 Laravel 中，有多种方法和辅助函数来协助你管理授权逻辑，本文档将会一一覆盖这些方法。

--------------------------------------------------------------------------------

## 9.2.2 定义权限（Abilities）

判断用户是否有权限执行给定动作的最简单方式就是使用`Illuminate\Auth\Access\Gate`类来定义一个`权限`。我们在`AuthServiceProvider`中定义所有权限。

例如，我们来定义一个接收当前`User`和`Post`模型的`update-post`权限，在该权限中，我们判断用户`id`是否和文章的`user_id`匹配：

```php
<?php

namespace App\Providers;

use Illuminate\Contracts\Auth\Access\Gate as GateContract;
use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

class AuthServiceProvider extends ServiceProvider{
    /**
     * 注册应用所有的认证/授权服务.
     *
     * @param  \Illuminate\Contracts\Auth\Access\Gate  $gate
     * @return void
     */
    public function boot(GateContract $gate)
    {
        parent::registerPolicies($gate);

        $gate->define('update-post', function ($user, $post) {
            return $user->id === $post->user_id;
        });
    }
}
```

注意我们并没有检查给定`$user`是否为`NULL`，当用户未经过登录认证或者用户没有通过`forUser`方法指定，`Gate`会自动为所有权限返回`false`。

- **基于类的权限**

  除了注册授权回调闭包之外，还可以通过传递包含权限类名和类方法的方式来注册权限方法，当需要的时候，该类会通过服务容器进行解析：

  ```
    $gate->define('update-post', 'PostPolicy@update');
  ```

- **拦截认证检查**

  有时候，你可能希望对指定用户授予所有权限，在这种场景中，需要使用`before`方法定义一个在所有其他授权检查之前运行的回调：

  ```
    $gate->before(function ($user, $ability) {
        if ($user->isSuperAdmin()) {
            return true;
        }
    });
  ```

  如果`before`回调返回一个非空结果，那么该结果则会被当做检查的结果。

  你也可以使用`after`方法定义一个在所有其他授权检查之后运行的回调，所不同的是，在`after`回调中你不能编辑授权检查的结果：

  ```
    $gate->after(function ($user, $ability, $result, $arguments) {
        //
    });
  ```

--------------------------------------------------------------------------------

## 9.2.3 检查权限

### 9.2.3.1 通过 Gate 门面

权限定义好之后，可以使用多种方式来`检查`。首先，可以使用`Gate`门面的`check`,`allows`, 或者`denies`方法。所有这些方法都接收权限名和传递给该权限回调的参数作为参数。你不需要传递当前用户到这些方法，因为`Gate`会自动附加当前用户到传递给回调的参数，因此，当检查我们之前定义的 `update-post`权限时，我们只需要传递一个`Post`实例到`denies`方法：

```php
<?php

namespace App\Http\Controllers;

use Gate;
use App\User;
use App\Post;
use App\Http\Controllers\Controller;

class PostController extends Controller{
    /**
     * 更新给定文章
     *
     * @param  int  $id
     * @return Response
     */
    public function update($id)
    {
        $post = Post::findOrFail($id);

        if (Gate::denies('update-post', $post)) {
            abort(403);
        }

        // 更新文章...
    }
}
```

当然，`allows`方法和`denies`方法是相对的，如果动作被授权会返回`true` ，`check`方法是 `allows`方法的别名。

- **为指定用户检查权限**

  如果你想要使用`Gate`门面判断非当前用户是否有权限，可以使用`forUser`方法：

  ```
    if (Gate::forUser($user)->allows('update-post', $post)) {
        //
    }
  ```

- **传递多个参数**

  当然，权限回调还可以接收多个参数：

  ```
    Gate::define('delete-comment', function ($user, $post, $comment) {
        //
    });
  ```

  如果权限需要多个参数，简单传递参数数组到`Gate`方法：

  ```
    if (Gate::allows('delete-comment', [$post, $comment])) {
        //
    }
  ```

### 9.2.3.2 通过 User 模型

还可以通过`User`模型实例来检查权限。默认情况下，Laravel 的`App\User`模型使用一个 `Authorizable`来提供两种方法：`can`和`cannot`。这两个方法的功能和`Gate`门面上的`allows`和 `denies`方法类似。因此，使用我们前面的例子，可以修改代码如下：

```php
<?php

namespace App\Http\Controllers;

use App\Post;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class PostController extends Controller{
    /**
     * 更新给定文章
     *
     * @param  \Illuminate\Http\Request  $request
     * @param  int  $id
     * @return Response
     */
    public function update(Request $request, $id)
    {
        $post = Post::findOrFail($id);

        if ($request->user()->cannot('update-post', $post)) {
            abort(403);
        }

        // 更新文章...
    }
}
```

当然，`can`方法和`cannot`方法相反：

```php
if ($request->user()->can('update-post', $post)) {
    // 更新文章...
}
```

### 9.2.3.3 在 Blade 模板引擎中检查

为了方便，Laravel 提供了`Blade`指令`@can`来快速检查当前用户是否有指定权限。

例如：

```
<a href="/post/{{ $post->id }}">View Post</a>

@can('update-post', $post)
    <a href="/post/{{ $post->id }}/edit">Edit Post</a>
@endcan
```

你还可以将`@can`指令和`@else`指令联合起来使用：

```
@can('update-post', $post)
    <!-- The Current User Can Update The Post -->
@else
    <!-- The Current User Can't Update The Post -->
@endcan
```

### 9.2.3.4 在表单请求中检查

你还可以选择在表单请求的`authorize`方法中使用`Gate`定义的权限。

例如：

```
/**
 * 判断请求用户是否经过授权
 *
 * @return bool
 */
public function authorize(){
    $postId = $this->route('post');
    return Gate::allows('update', Post::findOrFail($postId));
}
```

--------------------------------------------------------------------------------

## 9.2.4 策略类

### 9.2.4.1 创建策略类

由于在`AuthServiceProvider`中定义所有的授权逻辑将会变得越来越臃肿笨重，尤其是在大型应用中，所以 Laravel 允许你将授权逻辑分割到多个`策略`类中，策略类是`原生的PHP类`，基于授权资源对授权逻辑进行分组。

首先，让我们生成一个策略类来管理对`Post`模型的授权，你可以使用`Artisan`命令`make:policy`来生成该策略类。生成的策略类位于`app/Policies`目录：

```
php artisan make:policy PostPolicy
```

- **注册策略类**

  策略类生成后我们需要将其注册到`Gate`类。`AuthServiceProvider`包含了一个`policies`属性来映射实体及管理该实体的策略类。因此，我们指定`Post`模型的策略类是`PostPolicy`：

  ```php
    <?php

    namespace App\Providers;

    use App\Post;
    use App\Policies\PostPolicy;
    use Illuminate\Contracts\Auth\Access\Gate as GateContract;
    use Illuminate\Foundation\Support\Providers\AuthServiceProvider as ServiceProvider;

    class AuthServiceProvider extends ServiceProvider{
        /**
         * 应用的策略映射
         *
         * @var array
         */
        protected $policies = [
            Post::class => PostPolicy::class,
        ];

        /**
         * 注册所有应用认证/授权服务
         *
         * @param \Illuminate\Contracts\Auth\Access\Gate $gate
         * @return void
         */
        public function boot(GateContract $gate)
        {
            $this->registerPolicies($gate);
        }
    }
  ```

### 9.2.4.2 编写策略类

策略类生成和注册后，我们可以为授权的每个权限添加方法。例如，我们在`PostPolicy`中定义一个 `update`方法，该方法判断给定`User`是否可以更新某个`Post`：

```php
<?php

namespace App\Policies;

use App\User;
use App\Post;

class PostPolicy{
    /**
     * 判断给定文章是否可以被给定用户更新
     *
     * @param  \App\User  $user
     * @param  \App\Post  $post
     * @return bool
     */
    public function update(User $user, Post $post)
    {
        return $user->id === $post->user_id;
    }
}
```

你可以继续在策略类中为授权的权限定义更多需要的方法，例如，你可以定义`show`,`destroy`, 或者 `addComment`方法来认证多个`Post`动作。

_注意：所有策略类都通过服务容器进行解析，这意味着你可以在策略类的构造函数中类型提示任何依赖，它们将会自动被注入。_

- **拦截所有检查**

  有时候，你可能希望对指定用户授予所有权限，在这种场景中，需要使用`before`方法定义一个在所有其他授权检查之前运行的回调：

  ```php
    $gate->before(function ($user, $ability) {
        if ($user->isSuperAdmin()) {
            return true;
        }
    });
  ```

  如果`before`回调返回一个非空结果，那么该结果则会被当做检查的结果。

### 9.2.4.3 检查策略

策略类方法的调用方式和基于授权回调的闭包一样，你可以使用`Gate`门面，`User`模型，`@can`指令或者辅助函数`policy`。

- **通过 Gate 门面**

  `Gate`将会自动通过检测传递过来的类参数来判断使用哪一个策略类，因此，如果传递一个`Post`实例给`denies`方法，相应的，`Gate`会使用`PostPolicy`来进行动作授权：

  ```php
    <?php

    namespace App\Http\Controllers;

    use Gate;
    use App\User;
    use App\Post;
    use App\Http\Controllers\Controller;

    class PostController extends Controller{
        /**
         * 更新给定文章
         *
         * @param  int  $id
         * @return Response
         */
        public function update($id)
        {
            $post = Post::findOrFail($id);

            if (Gate::denies('update', $post)) {
                abort(403);
            }

            // 更新文章...
        }
    }
  ```

- **通过 User 模型**

  `User` 模型的`can`和`cannot`方法也会自动判断给定参数对应的策略类。这些方法提供了便利的方式来为应用接收到的任意`User`实例进行授权：

  ```
    if ($user->can('update', $post)) {
        //
    }

    if ($user->cannot('update', $post)) {
        //
    }
  ```

- **在 Blade 模板中使用**

  类似的，Blade 指令`@can`将会使用参数中有效的策略类：

  ```
    @can('update', $post)
        <!-- The Current User Can Update The Post -->
    @endcan
  ```

- **通过辅助函数 policy**

  全局的辅助函数`policy`用于为给定类实例接收策略类。例如，我们可以传递一个`Post`实例给帮助函数`policy`来获取相应的`PostPolicy`类的实例：

  ```
    if (policy($post)->update($user, $post)) {
        //
    }
  ```

--------------------------------------------------------------------------------

## 9.2.5 控制器授权

默认情况下，Laravel 自带的控制器基类`App\Http\Controllers\Controller`使用了 `AuthorizesRequests` trait，该 trait 提供了可用于快速授权给定动作的`authorize`方法，如果授权不通过，则抛出`HttpException`异常。

该`authorize`方法和其他多种授权方法使用方法一致，例如`Gate::allows`和`$user->can()`。因此，我们可以这样使用`authorize`方法快速授权更新`Post`的请求：

```php
<?php

namespace App\Http\Controllers;

use App\Post;
use App\Http\Controllers\Controller;

class PostController extends Controller{
    /**
     * 更新给定文章
     *
     * @param  int  $id
     * @return Response
     */
    public function update($id)
    {
        $post = Post::findOrFail($id);

        $this->authorize('update', $post);

        // 更新文章...
    }
}
```

如果授权成功，控制器继续正常执行；然而，如果`authorize`方法判断该动作授权失败，将会抛出 `HttpException`异常并生成带`403 Not Authorized`状态码的`HTTP响应`。正如你所看到的，`authorize`方法是一个授权动作、抛出异常的便捷方法。

`AuthorizesRequests` trait还提供了`authorizeForUser`方法用于授权非当前用户：

```
$this->authorizeForUser($user, 'update', $post);
```

- **自动判断策略类方法**

  通常，一个策略类方法对应一个控制器上的方法，例如，在上面的`update`方法中，控制器方法和策略类方法共享同一个方法名：`update`。

  正是因为这个原因，Laravel 允许你简单传递实例参数到`authorize`方法，被授权的权限将会自动基于调用的方法名进行判断。在本例中，由于`authorize`在控制器的`update`方法中被调用，那么对应的，`PostPolicy`上`update`方法将会被调用：

  ```
    /**
     * 更新给定文章
     *
     * @param  int  $id
     * @return Response
     */
    public function update($id){
        $post = Post::findOrFail($id);

        $this->authorize($post);

        // 更新文章...
    }
  ```

--------------------------------------------------------------------------------
