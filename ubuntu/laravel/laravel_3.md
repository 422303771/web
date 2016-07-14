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

![]()

#### 3.5.1 定义布局

#### 3.5.2 定义子视图

### 3.6 添加任务

#### 3.6.1 验证

#### 3.6.2 创建任务

#### 3.6.3 显示己存在的任务

### 3.7 删除任务

#### 3.7.1 添加删除按钮

#### 3.7.2 删除任务

