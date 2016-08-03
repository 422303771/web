# 5.3 控制器

## 5.3.1 简介

将所有的请求处理逻辑都放在单个`routes.php`中显示是不合理的，你也许还希望使用控制器类组织管理这些行为。

控制器可以将相关的HTTP请求封装到一个类中进行处理。

通常控制器存放在`app/Http/Controllers`目录中。

--------------------------------------------------------------------------------

## 5.3.2 基本控制器

下面是一个基本控制器类的例子。所有的laravel控制器应该继承，laravel自带的控制器基类`Controller`:

```php
<?php

namespace App\Http\Controllers;

use App\User;
use App\Http\Controllers\Controller;

class UserController extends Controller
{
    /**
     * 为指定用户显示详情
     *
     * @param int $id
     * @return Response
     */
    public function showProfile($id)
    {
        return view('user.profile', ['user' => User::findOrFail($id)]);
    }
}
```

我们可以像这样定义指向控制器动作的路由：

```php
Route::get('user/{id}', 'UserController@showProfile');
```

现在，如果一个请求匹配上面的路由URL，`UserController`的`showProfile`方法就会被执行。

当然，路由参数也会被传递给这个方法。

### 5.3.2.1 控制器 & 命名空间

在命名时，没有使用完整的控制器命名，而只定义了`App\Http\Controllers`之后的部分。

默认`RouteServiceProvider`会将一个路由群组载入`routes.php`文件，并且该路由群组指定了，群组中路由控制器所在的命名空间。

如果在`App\Http\Controllers`目录下选择使用PHP命名空间 或者 组织控制器，只需要使用相对于`App\Http\Controllers`命令空间的指定类名即可。因此，如果完整控制器类是`App\Http\Controllers\Photos\AdminController`,可以像这样注册路由：

```php
Route::get('foo', 'Photos\AdminController@method');
```

### 5.3.2.2 命名控制器路由

和闭包路由一样，可以指定控制器路由的名称：

```php
Route::get('foo', ['uses' => 'FooController@method', 'as' => 'name']);
```

你可以使用辅助函数`route`来为己命名的控制器路由生成对应的URL：

```php
$url = route('name');
```

--------------------------------------------------------------------------------

## 5.3.3 控制器中间件

中间件可以分配给控制器路由：

```php
Route::get('profile', [
    'middleware' => 'auth',
    'uses' => 'UserController@showProfile'
]);
```

但是，将中间件放在控制器构造函数中更方便，在控制器的构建函数中使用`middleware`方法可以很轻松的分配中间件给控制器。

甚至可以限定中间件应用到控制器的指定方法：

```php
class UserController extends Controller
{
    /**
     * 实例化一个新的 UserController 实例
     *
     * @return void
     */
    public function __construct()
    {
        $this->middleware('auth');
        $this->middleware('log', ['only' => ['fooAction', 'barAction']]);
        $this->middleware('subscribed', ['except' => ['fooAction', 'barAction']]);
    }
}
```

--------------------------------------------------------------------------------

## 5.3.4 RESTful 资源控制器

laravel的资源控制器使得构建围绕资源的`RESTful`控制器变得毫无痛苦。

例如，想要在应用中创建一个控制器，用于处理关于图片存储的HTTP请求，使用Artisan命令`make:controller`，可以快速创建这样的控制器：

```
php artisan make:controller PhotoController --resource
```

这个Artisan命令将会生成一个控制器文件`app/Http/Controllers/PhotoController.php`,这个控制器包含了每一个资源操作的方法。

接下来，可以为该控制器注册一个资源路由：

```
Route::resource('photo', 'PhotoController');
```

这个理由声明包含了处理图片资源RESTful 动作的多个路由，相应地，Artisan 生成的控制器也已经为这些动作设置了对应的处理方法。

### 5.3.4.1 资源控制器处理的动作

方法        | 路径                  | 动作      | 路由名称
:-------- | :------------------ | :------ | :------------
GET       | /photo              | index   | photo.index
GET       | /photo/create       | create  | photo.create
POST      | /photo              | store   | photo.store
GET       | /photo/{photo}      | show    | photo.show
GET       | /photo/{photo}/edit | edit    | photo.edit
PUT/PATCH | /photo/{photo}      | update  | photo.update
DELETE    | /photo/{photo}      | destroy | photo.destroy

_注意：HTTP表单不支持PUT、PATCH、DELETE请求，需要添加一个隐藏的`_method`文件来仿照这些动词。_

```html
<input type="hidden" name="_method" value="PUT">
```

### 5.3.4.2 只定义部分资源路由

声明资源路由时可以指定该路由处理的动作子集：

```php
Route::resource('photo', 'PhotoController',
['only' => ['index', 'show']]);

Route::resource('photo', 'PhotoController',
['except' => ['create', 'store', 'update', 'destroy']]);
```

### 5.3.4.3 命名资源路由

默认情况下，所有资源控制器动作都有一个路由名称，然而，我们可以通过传入`names`数组来覆盖这些默认的名字：

```php
Route::resource('photo', 'PhotoController',
                ['names' => ['create' => 'photo.build']]);
```

### 5.3.4.4 补充资源控制器

如果有必要在默认资源路由之外添加额外的路由到资源控制器，应该在调用`Route::resource`之前定义这些路由，否则，通过`resource`方法定义的路由可能无意中优于补充的额外路由：

```php
Route::get('photos/popular', 'PhotoController@method');
Route::resource('photos', 'PhotoController');
```

--------------------------------------------------------------------------------

## 5.3.5 依赖注入 & 控制器

### 5.3.5.1 构造函数注入

laravel使用服务容器解析所有的Laravel控制器，因此，可以在控制器的构造函数中声明任何依赖，这些依赖会被自动解析并注入到控制器实例中：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Routing\Controller;
use App\Repositories\UserRepository;

class UserController extends Controller
{
    /**
     * The user repository instance.
     */
    protected $users;

    /**
     * 创建新的控制器实例
     *
     * @param UserRepository $users
     * @return void
     */
    public function __construct(UserRepository $users)
    {
        $this->users = $users;
    }
}
```

当然，还可以对任何的`laravel contact` 使用类别提示。若容器能解析它，就可以使用型别提示。

### 5.3.5.2 方法注入

除了构造函数注入外，还可以在控制器的动作方法中进行依赖的类型提示。

例如，可以对`Illuminate\Http\Request`实例，其中的一个方法使用类别提示：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;

class UserController extends Controller
{
    /**
     * 儲存一個新的使用者。
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        $name = $request->input('name');

        //
    }
}
```

如果控制器方法期望输入路由参数，只需要将路由参数放到其他依赖之后，例如，如果你的路由定义如下：

```php
Route::put('user/{id}', 'UserController@update');
```

你需要通过定义控制器方法如下所示来类型提示 `Illuminate\Http\Request` 并访问路由参数 `id`：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

class UserController extends Controller
{
    /**
     * 更新指定用户
     *
     * @param Request $request
     * @param int $id
     * @return Response
     * @translator http://laravelacademy.org
     */
    public function update(Request $request, $id)
    {
        //
    }
}
```

--------------------------------------------------------------------------------

## 5.3.6 路由缓存

**使用控制器路由的重要性**

_注意：路由缓存不会作用于基于闭包的路由。要使用路由缓存，必须将闭包路由转化为控制器路由。_

如果你的应用完全基于控制器路由，可以使用 Laravel 的路由缓存，使用路由缓存将会极大减少注册所有应用路由所花费的时间开销，在某些案例中，路由注册速度甚至能提高100倍！想要生成路由缓存，只需执行 Artisan 命令·`route:cache`:

```
php artisan route:cache
```

就这么简单！你的缓存路由文件现在取代 `app/Http/routes.php` 文件被使用，记住，如果你添加新的路由需要重新生成路由缓存。因此，只有在项目部署阶段才需要运行 `route:cache` 命令。

想要移除缓存路由文件，使用 `route:clear` 命令即可：

```
php artisan route:clear
```

--------------------------------------------------------------------------------
