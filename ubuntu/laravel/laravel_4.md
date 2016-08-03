# 4 快速入门进阶版

## 4.1\. 安装

### 4.1.1 使用composer

```
$ composer create-project laravel/laravel quickstart --prefer-dist
```

### 4.1.2 使用git克隆完整包

```
$ git clone https://github.com/laravel/quickstart-intermediate quickstart
$ cd quickstart
$ composer install
$ php artisan migrate
```

## 4.2 准备数据库

### 4.2.1 数据迁移

需要用到users表与tasks表。

users表的已经在`database/migratiions`目录下。

tasks表，需要使用命令创建一个：

```
$ php artisan make:migration create_tasks_table --create=tasks
```

在生成的文件中加入

```php
$table->integer('user_id')->index();
$table->string('name');
```

`->index()`标识增加索引。`user_id`用于建立`tasks`表与`user`表之间的关联。

使用下方命令导入数据库

```
$ php artisan migrate
```

### 4.2.2 Eloquent模型

user模型

laravel中自带了`user`模块。

Task模型

使用下方命令创建`task`模型。

```
$ php artisan make:model Task
```

在`Task`模型中,声明`name`属性支持`批量赋值`。

批量赋值：将一个数组发送到模型类用于创建新模型实例是使用。

laravel有两种`批量赋值`属性，一种是`$fillable`可以通过批量赋值进行赋值。另一种是`$guarded`在批量赋值时会被过滤掉。

添加到`app/Task.php`

```
protected $fillable = ['name'];
```

### 4.2.3 Eloquent关联关系

tasks关联关系

在`user`模型中定义`tasks`关联关系。这里使用一对多关系，laravel的`ELoquent`中提供了`hasMany`方法。

在`app/user.php`文件中添加：

```
use App\Task;

public function tasks()
{
    return $this->hasMany(Task::class);
}
```

user关联关系

要定义与`hasMany`相对的关联关系，需要在子模型中定义一个关联方法去调用`belongsTo`方法:

添加到`app/Task.php`

```
use App\User;

public function user()
{
    return $this->belongsTo(User::class);
}
```

## 4.3 路由

在`routes.php`中可以使用闭包定义所有的业务逻辑。实际上，大部分应用都会使用控制器来组织路由。

### 4.3.1 显示视图

使用`view`函数从路由中返回一个模板：

```php
Route::get('/', function () {
    return view('welcome');
});
```

### 4.3.2 用户认证

web中用户认证工作是非常乏味的，laravel提供一个`auth`组件让他变得轻松。

可以直接使用Artisan指令做完成`auth`的创建。

```
$ php artisan make:auth --views
```

随后只需要将`auth`添加到路由列表。

```php
Route::auth();
```

### 4.3.3 任务控制器

接下来建立`TaskController`控制器，来处理任务。默认情况下控制器放置在`app/Http/controllers`目录下。使用下方命令创建：

```
$ php artisan make:controller TaskController
```

现在添加一些对应控制器的路由，在`app/Http/routes.php`中添加：

```php
Route::get('/tasks', 'TaskController@index');
Route::post('/task', 'TaskController@store');
Route::delete('/task/{task}', 'TaskController@destroy');
```

**设置所有任务路由需要登录才能访问**

我们希望用户必须登陆到系统才能创建新任务。所以需要限制访问用户，为登陆用户。

laravel使用中间件来处理这种限制。

```php
    public function __construct()
    {
        $this->middleware('auth');
    }
```

## 4.4 创建布局与视图

布局方案与之前的版本相同。

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/laravel/img/basic-overview.png)

### 4.4.1 定义布局

共用模板在`resources/views/layouts/app.blade.php`。

`@yield('content')`为使用blade模板要插入的功能模块。

### 4.4.2 定义子视图

随后定义`resources/views/tasks/index.blade.php`。它会对应`TaskController`控制器的`index`方法。

随后在`TaskController`控制其中添加`index`方法的返回视图。

```php

public function index(Request $request)
{
    return view('tasks.index');
}
```

## 4.5 添加任务

### 4.5.1 验证表单输入

编写`TaskController@store`路由对应的处理方法，处理一个表单请求并创建一个新任务。

```php
public function store(Request $request){
    $this->validate($request, [
        'name' => 'required|max:255',
    ]);

    // Create The Task...
}
```

在控制器中，可以直接使用`ValidatesRequests`类中的`validate`方法。

而不用手动判断验证失败后的重定向，如果验证失败，用户会自动被重定向到来源页面，而错误信息也会存放到一次性`Session`中。

**$errors**

使用`@include('common.errors')`来渲染表单验证错误信息。

文件位置`resources/views/common/errors.blade.php`

```html
@if (count($errors) > 0)
    <!-- Form Error List -->
    <div class="alert alert-danger">
        <strong>Whoops! Something went wrong!</strong>

        <br><br>

        <ul>
        @foreach ($errors->all() as $error)
            <li>{{ $error }}</li>
        @endforeach
        </ul>
    </div>
@endif
```

### 4.5.2 创建任务

验证完成后，要新建一个任务。当任务新建完成，页面会跳转到`/tasks`。

laravel的关联提供了`save`方法，该方法接收一个关联模型实例，并且会在保存的数据库之前自动设置外键值到关联模型上。

这里`save`方法会自动将当前用户的，用户ID赋予给定的`user_id`属性。通过`$request->user()`获取当前用户实例：

```php
    $request->user()->tasks()->create([
        'name' => $request->name,
    ]);

    return redirect('/tasks');
```

## 4.6 显示己存在的任务

现在需要编辑`TaskController@index`传递所有已存在任务到视图。`view`函数接收一个数组作为第二个参数，数组中的每个值都会在视图中作为变数。

```php
public function index(Request $request)
{
    $tasks = Task::where('user_id', $request->user()->id)->get();

    return view('tasks.index', [
        'tasks' => $tasks,
    ]);
}
```

### 4.6.1 依赖注入

laravel的服务容器是整个框架中最重要的特性。

**创建Repository**

正如之前说的，我们要定义一个`TaskRepository`来处理所有对`Task`模式的数据访问，随着应用的增长，需要在应用中共享一些Eloquent查询时，就变得特别有用。

创建`app/Repositories`目录并在其中创建一个`TaskRepository`类。记住，laravel项目的`app`文件夹下的所有目录都是使用PSR-4自动加载标准，所以可以随意创建需要的目录:

```php
<?php

namespace App\Repositories;

use App\User;
use App\Task;

class TaskRepository{
    /**
     * Get all of the tasks for a given user.
     *
     * @param User $user
     * @return Collection
     */
    public function forUser(User $user)
    {
        return Task::where('user_id', $user->id)
            ->orderBy('created_at', 'asc')
            ->get();
    }
}
```

**注入Repository**

Repository创建好之后，通过在`TaskController`的构造函数以类型提示的方式注入该Repository,然后就可以在index方法中使用。

由于laravel使用容器来解析所有控制器，所以依赖会被自动注入到控制器：

```php
<?php

namespace App\Http\Controllers;

use App\Task;use App\Http\Requests;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;
use App\Repositories\TaskRepository;

class TaskController extends Controller{
    /**
     * The task repository instance.
     *
     * @var TaskRepository
     */  
    protected $tasks;

    /**
     * Create a new controller instance.
     *
     * @param TaskRepository $tasks
     * @return void
     */
    public function __construct(TaskRepository $tasks)
    {
        $this->middleware('auth');
        $this->tasks = $tasks;
    }

    /**
     * Display a list of all of the user's task.
     *
     * @param Request $request
     * @return Response
     */
    public function index(Request $request)
    {
        return view('tasks.index', [
            'tasks' => $this->tasks->forUser($request->user()),
        ]);
    }
```

### 4.6.2 显示任务

在`tasks/index.blade.php`中以表格形式显示所有任务。Blade中使用`@foreach`处理循环数据。

## 4.7 删除任务

### 4.7.1 添加删除按钮

当一个`DELETE /task`请求被发送到应用，它会触发`TaskController@destroy`方法：

在表单中同样要使用方法欺骗。

### 4.7.2 路由模型绑定

定义`TaskController`的`destroy`方法。但是首先，重新查看路由的定义以及控制器方法。

### 4.7.3 用户授权

现在要将`Task`实例注入到`destroy`方法。然而为了保证当前登录用户是给定任务的用。

例如：一些恶意请求可能尝试通过传统随机任务ID到`/tasks/{task}`链接删除另一个用户的任务。

所有需要使用laravel的授权功能来确保，当前登录用户拥有操作`Task`实例的权限。

**创建Policy**

laravel使用策略来将授权组织到单个类中，通常，每个策略都对应一个模型。

因此，使用Artisan命令创建一个`TaskPolicy`,生成的文件位于`app/Policies/TaskPolicy.php`:

```
$ php artisan make:policy TaskPolicy
```

随后将`destroy`方法添加到策略中，该方法会获取一个`user`实例和`task`实例，检查`用户ID`和任务的`user_id`是否相同。

实际上，所有的策略方法都会返回`true`或`false`:

```php
<?php

namespace App\Policies;

use App\User;
use App\Task;
use Illuminate\Auth\Access\HandlesAuthorization;

class TaskPolicy{
    use HandlesAuthorization;

    /**
     * Determine if the given user can delete the given task.
     *
     * @param User $user
     * @param Task $task
     * @return bool
     */
    public function destroy(User $user, Task $task)
    {
        return $user->id === $task->user_id;
    }
}
```

最后，需要关联`Task`模型和`TaskPolicy`，通过在`app/Providers/AuthServiceProvider.php`的`policies`属性添加注册实现。

注册后会告知Laravel无论何时尝试授权到`Task`实例时，该使用哪个策略类进行判断：

```php
protected $policies = [
    'App\Task' => 'App\Policies\TaskPolicy',
];
```

**授权动作**

编写好策略后，在`destroy`方法中使用它。所有的laravel控制器都可以调用`authorize`方法，该方法由`AuthorizesRequest`提供：

`app/Controllers/TaskController.php`文件。

```php
public function destroy(Request $request, Task $task){
    $this->authorize('destroy', $task);
    // Delete The Task...
}
```

### 4.7.4 删除任务

最后在`destroy`方法中实际删除任务。使用Eloquent的`delete`方法从数据库中删除给定的模型实例。

删除后返回`/tasks`连接：

`app/Controllers/TaskController.php`文件中完整的`TaskController@destroy`方法。

```php
public function destroy(Request $request, Task $task){
    $this->authorize('destroy', $task);
    $task->delete();
    return redirect('/tasks');
}
```
