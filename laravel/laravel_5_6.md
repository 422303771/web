<!--toc-->

- [5.6 视图](#56-视图)
	- [5.6.1 基本使用](#561-基本使用)
		- [5.6.1.1 判断视图是否存在](#5611-判断视图是否存在)
	- [5.6.2 视图数据](#562-视图数据)
		- [5.6.2.1 传递数据到视图](#5621-传递数据到视图)
		- [5.6.2.2 在视图间共享数据](#5622-在视图间共享数据)
	- [5.6.3 视图Composer](#563-视图composer)
		- [5.6.3.1 添加 Composer 到多个视图](#5631-添加-composer-到多个视图)
	- [5.6.4 视图创建器](#564-视图创建器)

<!-- tocstop -->

----

# 5.6 视图

## 5.6.1 基本使用

视图包含的HTML代码并将控制器逻辑和表面逻辑进行分离。视图文件存放在`resources/views`目录。

下面是一个简单视图：

```html
<!-- 该视图存放 resources/views/greeting.php -->

<html>
    <body>
        <h1>Hello, <?php echo $name; ?></h1>
    </body>
</html>
```

由于这个视图存放在`resources/views/greeting.php`,可以在全局的辅助函数`view`中这样返回它：

```php
Route::get('/', function () {
    return view('greeting', ['name' => 'James']);
});
```

`view`的第一个参是`resources/views`目录下相应的视图文件的名字，第二个参数是一个数组，该数组包含了在视图中的有效数据。

例子中，传递的是一个`name`变量，在视图中通过执行`echo`将其显示出来。

当然，视图还可以嵌套在`resources/views`的子目录中，用`.`来引用嵌套视图。

例如，视图存放路径是`resources/views/admin/profile.php`,那可以这样应用它：

```
return view('admin.profile', $data);
```

### 5.6.1.1 判断视图是否存在

如果需要判断视图是否存在，可调用不带参数的`view`之后，使用`exists`方法，如果视图存在则返回`ture`:

```php
if (view()->exists('emails.customer')) {
    //
}
```

调用不带参数的`view`时，将会返回一个`Illuminate\Contracts\View\Factory`实例，从而调用该类上所有方法。

--------------------------------------------------------------------------------

## 5.6.2 视图数据

### 5.6.2.1 传递数据到视图

在上述例子中可以看到，可以简单通过数组方式将数据传递到视图：

```
return view('greetings', ['name' => 'Victoria']);
```

以这种方式传递数据，`$data`应该是一个键值对应该数组，在视图中，就可以使用响应的键来访问数组值。

例如`<?php echo $key; ?>`。还可以通过`with`方法添加独立的数据片段到视图：

```php
$view = view('greeting')->with('name', 'Victoria');
```

### 5.6.2.2 在视图间共享数据

有时候需要在所有视图之间共享数据片段，这时可以使用视图类的`share`方法，通常，需要在服务提供者的`boot`方法中调用`share`方法，你可以将其添加到`AppServiceProvider`或生成独立的服务提供者来存放它们：

```php
<?php

namespace App\Providers;

class AppServiceProvider extends ServiceProvider
{
    /**
     * 启动所有应用服务
     *
     * @return void
     */
    public function boot()
    {
        view()->share('key', 'value');
    }

    /**
     * 注册服务提供者
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

--------------------------------------------------------------------------------

## 5.6.3 视图Composer

视图Composer就是在视图被渲染前，会呼叫的闭包或类别方法。

如果想在每次渲染都绑定一些数据，可以使用视图Composer将这种的程序逻辑放到同一个地方。

首先在服务提供者中注册视图Composer。

之后将使用`view`访问`Illuminate\Contracts\View\Factory`的底层实现。

laravel不会包含默认的视图Composer目录，可以随意设置路径。

例如，创建一个`App\Http\ViewComposers`目录：

```php
<?php

namespace App\Providers;

use Illuminate\Support\ServiceProvider;

class ComposerServiceProvider extends ServiceProvider
{
    /**
     * 在容器中注册绑定.
     *
     * @return void
     * @author http://laravelacademy.org
     */
    public function boot()
    {
        // 使用基于类的composers...
        view()->composer(
            'profile', 'App\Http\ViewComposers\ProfileComposer'
        );

        // 使用基于闭包的composers...
        view()->composer('dashboard', function ($view) {
        });
    }

    /**
     * 注册服务提供者.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```

如果创建一个新的服务提供者包含视图Composer注册，需要添加该服务提供者到配置文件`config/app.php`的`providers`数组中。

现在我们已经注册了Composer，每次`profile`视图被渲染时都会执行`PrefileComposer@compose`,接下来定义Composer类：

```php
<?php

namespace App\Http\ViewComposers;

use Illuminate\Contracts\View\View;
use Illuminate\Users\Repository as UserRepository;

class ProfileComposer
{
    /**
     * 用户仓库实现.
     *
     * @var UserRepository
     */
    protected $users;

    /**
     * 创建一个新的属性composer.
     *
     * @param UserRepository $users
     * @return void
     */
    public function __construct(UserRepository $users)
    {
        // Dependencies automatically resolved by service container...
        $this->users = $users;
    }

    /**
     * 绑定数据到视图.
     *
     * @param View $view
     * @return void
     */
    public function compose(View $view)
    {
        $view->with('count', $this->users->count());
    }
}
```

视图被渲染前，Composer类的composer方法被调用，同时`Illuminate\Contracts\View\View`被注入该方法，从而可以使用`with`方法来绑定数据到视图。

_注意：所有视图Composer都通过服务容器被解析，所以可以在Composer类的构造函数中声明任何需要的依赖。_

### 5.6.3.1 添加 Composer 到多个视图

可以传递视图数组作为`composer`方法的第一个参数，来一次将视图Composer添加到多个视图：

```php
view()->composer(
    ['profile', 'dashboard'],
    'App\Http\ViewComposers\MyViewComposer'
);
```

`composer`方法接受`*`通配符，从而允许将一个Composer添加到所有视图:

```php
view()->composer('*', function ($view) {
    //
});
```

--------------------------------------------------------------------------------

## 5.6.4 视图创建器

视图创造器和视图Composer非常类似。不同在于，前者在视图实例化之后立即失效而不是等到视图即将渲染。

使用`create`方法可注册一个视图创建器：

```
view()->creator('profile', 'App\Http\ViewCreators\ProfileCreator');
```
