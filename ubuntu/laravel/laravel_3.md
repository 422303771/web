## 3. 跟随快速入门

[官方地址](https://laravel.com/docs/5.2/quickstart)

[中文版地址](http://laravelacademy.org/post/3291.html)

[台湾版地址](https://laravel.tw/docs/5.2/quickstart)

开始前先创建一个新的数据库，并添加到.`env`文件中。

### 3.1 创建quickstart项目

#### 3.1.1 使用composer

	$ composer create-project laravel/laravel quickstart --prefer-dist

#### 3.1.2 克隆整个快速入门文档

	$ git clone https://github.com/laravel/quickstart-basic quickstart
	$ cd quickstart
	$ composer install
	$ php artisan migrate

### 3.2 准备数据库

laravel有一种简单的定义与修改数据表的方法。

现在使用`make:migration`生成`tasks`新的数据表：

	$ php artisan make:migration create_tasks_table --create=tasks

`make:migration`自动添加了`id`与`时间戳`。

手动增加一个条码：

```php 
	$table->string('name');
``` 
随后使用下方命令将`tasks`写入数据表。

	$ php artisan migrate

*注意：如果数据库中以及存在`tasks`数据表，会报错。*

### 3.3 Eloquent ORM模型

laravel 使用默认ORM是Eloquent，[ORM：物件关联对应]，每一个Eloquent模型都有一个对应的数据表。

所有创建`tasks`对应的`Task`模型，使用`Artisan`命令来生成模型。

	$ php artisan make:model Task

模型在`app`目录下，默认情况下，模型类是空的。不需要说明Eloquent模型要对应哪张数据表，它会假设数据表是模型的复数形态。

这里`Task`对应的是`tasks`。

### 3.4 路由

#### 3.4.1 建置路由

默认情况下，所有的路由都定义在`app/Http/routes.php`

这个需要至少三个路由：显示所有任务的路由，添加新任务的路由，以及删除己存在任务的路由。

下面再`app/Http/routse.php`中创建这三个路由。
	
```php
<?php

use App\Task;
use Illuminate\Http\Request;

/**
 * Display All Tasks
 */
Route::get('/', function () {
   //
});

/**
 * Add A New Task
 */
Route::post('/task', function (Request $request) {
    //
});

/**
 * Delete An Existing Task
 */
Route::delete('/task/{id}', function ($id) {
    //
```

#### 3.4.2 显示视图

laravel的全部，HTML模板都存放在`resources/view`目录下，使用`view`函数从路由中返回一个模板。

```php
Route::get('/', function () {
    return view('tasks');
});
```

现在要在创建一个对应的视图模板`resources/views/tasks.blade.php`作为`View`对象。

### 3.5 创建布局&视图

下图中使用了`Bootstrap CSS`样式

![](https://raw.githubusercontent.com/422303771/web/master/ubuntu/laravel/img/basic-overview.png)

#### 3.5.1 定义布局

**几乎所有的web应用都在不同的页面中共享同一个布局。**

例如，这个应用在视图顶部的导航条，这个导航条在每个页面都会出现。

laravel通过在每个页面中使用Blade布局让共享这些公共特性变得简单。

在`resources/views/layouts/app.blade.php`中定义一个新布局，`.blade.php`表明使用Blade模板引擎来渲染视图。

也可以使用PHP原生模板然而，Blade提供的标签语法可以编写简洁的模板。

`app.blade.php`的内容如下：

```html
// resources/views/layouts/app.blade.php
<!DOCTYPE html><html lang="en">
    <head>
        <title>Laravel Quickstart - Basic</title>

        <!-- CSS And JavaScript -->
    </head>

    <body>
        <div class="container">
            <nav class="navbar navbar-default">
                <!-- Navbar Contents -->
            </nav>
        </div>

        @yield('content')
    </body>
</html>
```
布局中的`@yield('content')`部分，是一个Blade指令，用于指定继承布局的子页面，在这里可以注入自己的内容。

#### 3.5.2 定义子视图

现在定义`resources/views/tasks.blade.php`视图。`Bootstrap CSS`的样本文件可以到[github](https://github.com/laravel/quickstart-basic)下载

`tasks.blade.php`内容如下：

```html
//resources/views/tasks.blade.php

@extends('layouts.app')

@section('content')

    <!-- Bootstrap Boilerplate... -->

    <div class="panel-body">
        <!-- Display Validation Errors -->
        @include('common.errors')

        <!-- New Task Form -->
        <form action="/task" method="POST" class="form-horizontal">
            {{ csrf_field() }}

            <!-- Task Name -->
            <div class="form-group">
                <label for="task" class="col-sm-3 control-label">Task</label>

                <div class="col-sm-6">
                    <input type="text" name="name" id="task-name" class="form-control">
                </div>
            </div>

            <!-- Add Task Button -->
            <div class="form-group">
                <div class="col-sm-offset-3 col-sm-6">
                    <button type="submit" class="btn btn-default">
                        <i class="fa fa-plus"></i> Add Task
                    </button>
                </div>
            </div>
        </form>
    </div>

    <!-- TODO: Current Tasks -->
@endsection
```

**注意：**

使用`@extends`告诉Blade要使用在`resources/views/layouts/app.blade.php`的布局。

在`@section('content')`与`@endsection`之间的内容会被注入到`app.blade.php`的`@yield('contents')`位置。

` @include('common.errors')`指令将会加载`resources/views/common/errors.blade.php`中的内容，目前还没有定义。

现在可以设置路由：

```php
Route::get('/', function () {
    return view('tasks');
});
```

### 3.6 添加任务

#### 3.6.1 验证

验证表单，在`POST /task`路由中编写代码，处理表单请求，我们需要验证表单输入，然后才能创建一个新任务。

对这个表单而言，将`name`字段设置为必填项，而长度不超过255个字符。如果表单验证失败，将会跳转到前一个页面，并将错误信息存入一次性的`Session`中：

```php
Route::post('/task', function (Request $request) {
    $validator = Validator::make($request->all(), [
        'name' => 'required|max:255',
    ]);

    if ($validator->fails()) {
        return redirect('/')
            ->withInput()
            ->withErrors($validator);
    }

    // Create The Task...
});
```
**$errors**

`->withErrors($validator)`将验证错误信息存放在一次性的session中，以便视图通过`$errors`访问。



`errors.blade.php`文件

```php
// resources/views/common/errors.blade.php

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

**注意：**

`errors`变量可用于每个laravel的视图中。如果没有验证错误信息存在，那么他就是一个空的`ViewErroeBag`实例。

#### 3.6.2 创建任务

接下来开始创建一个新任务。一旦新任务创建成功，页面会跳转到`/`。要创建任务，可以有`Eloquent`模式提供的`save`方法：

```php
Route::post('/task', function (Request $request) {
    $validator = Validator::make($request->all(), [
        'name' => 'required|max:255',
    ]);

    if ($validator->fails()) {
        return redirect('/')
            ->withInput()
            ->withErrors($validator);
    }

    $task = new Task;
    $task->name = $request->name;
    $task->save();

    return redirect('/');
});
```

#### 3.6.3 显示己存在的任务

添加代码到视图来显示所有任务列表。

需要编辑/路由传递所有已存在任务到视图，view函数接收一个数组作为第二个参数，我们可以将数据通过该数组传递到视图中：

```php
Route::get('/', function () {
    $tasks = Task::orderBy('created_at', 'asc')->get();

    return view('tasks', [
        'tasks' => $tasks
    ]);
});
```

数据被传递到视图后，我们可以在`tasks.blade.php`中以表格形式显示所有任务。Blade中使用`@foreach`处理循环数据：

```php
@extends('layouts.app')

@section('content')
    <!-- Create Task Form... -->

    <!-- Current Tasks -->
    @if (count($tasks) > 0)
        <div class="panel panel-default">
            <div class="panel-heading">
                Current Tasks
            </div>

            <div class="panel-body">
                <table class="table table-striped task-table">

                    <!-- Table Headings -->
                    <thead>
                        <th>Task</th>
                        <th>&nbsp;</th>
                    </thead>

                    <!-- Table Body -->
                    <tbody>
                    @foreach ($tasks as $task)
                        <tr>
                            <!-- Task Name -->
                            <td class="table-text">
                                <div>{{ $task->name }}</div>
                            </td>

                            <td>
                                <!-- TODO: Delete Button -->
                            </td>
                        </tr>
                    @endforeach
                    </tbody>
                </table>
            </div>
        </div>
    @endif
@endsection
```

### 3.7 删除任务

#### 3.7.1 添加删除按钮

我们在`tasks.blade.php`视图中留了一个“TODO”注释用于放置删除按钮。当删除按钮被点击时，`DELETE /task`请求被发送到应用后台：

```HTML
<tr>
    <!-- Task Name -->
    <td class="table-text">
        <div>{{ $task->name }}</div>
    </td>

    <!-- Delete Button -->
    <td>
        <form action="/task/{{ $task->id }}" method="POST">
            {{ csrf_field() }}
            {{ method_field('DELETE') }}

            <button>Delete Task</button>
        </form>
    </td>
</tr>
```
**方法伪造**

尽管我们使用的路由是`Route::delete`，但我们在删除按钮表单中使用的请求方法为`POST`，HTML表单只支持`GET`和`POST`两种请求方式，因此我们需要使用某种方式来伪造DELETE请求。

我们可以在表单中通过输出`method_field('DELETE')`来伪造`DELETE`请求，该函数生成一个隐藏的表单输入框，然后Laravel识别出该输入并使用其值覆盖实际的HTTP请求方法。生成的输入框如下：

<input type="hidden" name="_method" value="DELETE">

#### 3.7.2 删除任务

最后，让我们添加业务逻辑到路由中执行删除操作，我们可以使用`Eloquent`提供的`findOrFail`方法从数据库通过ID获取模型实例，如果不存在则抛出404异常。获取到模型后，我们使用模型的`delete`方法删除该模型在数据库中对应的记录。记录被删除后，跳转到`/`页面:

```php
Route::delete('/task/{id}', function ($id) {
    Task::findOrFail($id)->delete();
    return redirect('/');
});
```

