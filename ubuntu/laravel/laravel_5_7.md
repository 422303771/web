# 5.7 Blade 模板

## 5.7.1 简介

Blade 是 Laravel 提供的一个非常简单但很强大的模板引擎，不同于其他流行的PHP模板引擎，Blade在视图中并不约束你使用PHP原生代码。所有的Blade视图都会被编译成原生PHP代码并缓存起来直到被修改，这意味着对应用的性能而言Blade基本上是零开销。Blade视图文件使用`.blade.php`文件扩展并存放在`resources/views`目录下。

--------------------------------------------------------------------------------

## 5.7.2 模板继承

### 5.7.2.1 定义页面布局

使用Blade的两个最大的优点是模板继承和切片，开始之前先看一个例子。

首先，检测一个主页面布局，由于大多数web应用在不同页面中使用同一个布局，可以很方便的将这个布局定义为一个单独的Blade页面：

```html
<!-- 存放在 resources/views/layouts/master.blade.php -->

<html>
    <head>
        <title>App Name - @yield('title')</title>
    </head>
    <body>
        @section('sidebar')
            This is the master sidebar.
        @show

        <div class="container">
            @yield('content')
        </div>
    </body>
</html>
```

正如所看到的，该文件包含典型的HTML标记，然而，注意`@section`和`@yield`指令，前者正如其名字所暗示的，定义了一个内容的片段，而后者用于显示给定片段的内容。

现在我们已经为应用定义了一个布局，接下来让我们定义继承该布局的子页面。

### 5.7.2.2 扩展页面布局

定义子页面的时候，可以使用Blade的`@extends`指令来指定子页面所继承的布局，继承一个Blade布局的视图将会使用`@section`指令注入内容到布局的片段中，记住，如上面例子所示，这些片段的内容将会显示在布局中使用`@yield`的地方：

```php
<!-- 存放在 resources/views/child.blade.php -->

@extends('layouts.master')

@section('title', 'Page Title')

@section('sidebar')
    @parent

    <p>This is appended to the master sidebar.</p>
@endsection

@section('content')
    <p>This is my body content.</p>
@endsection
```

在本例中，`sidebar`片段使用`@parent`指令来追加内容到布局中`sidebar`,`@parent`指令在视图渲染时将会被布局中的内容替换。

当然，和原始PHP视图一样，Blade视图可以通过`view`方法直接从路由中返回：

```php
Route::get('blade', function () {
   return view('child');
});
```

--------------------------------------------------------------------------------

## 5.7.3 数据显示

可以通过两个花括号包裹变量来显示传递到视图的数据，比如，如果给出如下路由：

```php
Route::get('greeting', function () {
    return view('welcome', ['name' => 'Samantha']);
});
```

那么可以通过如下方式显示`name`变量的内容:

```
Hello, {{ $name }}.
```

当然，不限制显示到视图中的变量内容，你还可以输出任何PHP函数，实际上，可以将任何PHP代码放到Blade模板语句中：

```
The current UNIX timestamp is {{ time() }}.
```

_注意：Blade的`{{}}`语句已经经过PHP的`htmlentities`函数处理以避免XSS攻击。_

### 5.7.3.1 Blade & JavaScript 框架

由于很多Javascript框架也是用花括号表示，要显示在浏览器中的表达式，可以使用`@`告诉Blade渲染引擎该表达式应该保持原生格式不作改动。例如：

```html
<h1>Laravel</h1>

Hello, @{{ name }}.
```

这里`@`符合会被Blade移除。而且，Blade引擎会保留`{{name}}`表单式，如此一来可让其它Javascript框架使用。

### 5.7.3.2 输出存在的数据

有时候想要输出一个变量，但是不确定该变量是否被设置，我们可以通过如下PHP代码：

```
{{ isset($name) ? $name : 'Default' }}
```

处理上方的方法，Blade还提供了更简单的方式：

```
{{ $name or 'Default' }}
```

在这里，如果`$name`变量存在，将会显示，否则将显示`Default`

### 5.7.3.3 显示原生数据

默认情况下，Blade的`{{ }}`语法已经通过PHP的`htmlentities`函数处理以避免XSS攻击，如果不想要数据被处理，可以使用如下语法：

```
Hello, {!! $name !!}.
```

_注意：对于用户输入的内容要当心，要使用双花括号以避免直接输出HTML代码。_

--------------------------------------------------------------------------------

## 5.7.4 流程控制

除了模板继承和数据显示之外，Blade还为常用的PHP流程提供了便利操作，比如条件语句和循环，这些快捷操作提供了一个干净，简单的方式来处理PHP的流程控制，同时保持和PHP相应语句相似。

### 5.7.4.1 If 语句

可以使用`@if`,`@elseif`,`@else`,`@endif`,来构造if语句，这些指令函数和PHP的相同：

```php
@if (count($records) === 1)
    I have one record!
@elseif (count($records) > 1)
    I have multiple records!
@else
    I don't have any records!
@endif
```

为了方便，Blade还提供了`@unless`指令：

```php
@unless (Auth::check())
    You are not signed in.
@endunless
```

### 5.7.4.2 循环

除了条件语句，Blade还提供了简单指令处理PHP支持的循环结构，同样，这些指令函数和PHP一样：

```php
@for ($i = 0; $i < 10; $i++)
    The current value is {{ $i }}
@endfor

@foreach ($users as $user)
    <p>This is user {{ $user->id }}</p>
@endforeach

@forelse ($users as $user)
    <li>{{ $user->name }}</li>
    @empty
    <p>No users</p>
@endforelse

@while (true)
    <p>I'm looping forever.</p>
@endwhile
```

### 5.7.4.3 包含子视图

Blade的`@include`指令允许你很简单的在一个视图中包含另一个Blade视图，所有父级视图中变量在被包含的子视图中依然有效：

```html
<div>
    @include('shared.errors')

    <form>
        <!-- Form Contents -->
    </form>
</div>
```

尽管被包含的视图继承所有父视图中的数据，还可以传递额外参数到被包含的视图：

```
@include('view.name', ['some' => 'data'])
```

_注意：不要再Blade视图中使用`__DIR__`和`__FIFL__`常量，因为他们会指向缓存视图的路径。_

### 5.7.4.4 为集合渲染视图

使用Blade的`@each`指令将循环或引入 压缩成一行：

```
@each('view.name', $jobs, 'job')
```

第一个参数为：对数组或集合的每个元素渲染的局部视图。

第二个参数为：要迭代的数组或集合。

第三个参数为：迭代时被分配到视图中的变数名称。

举例来说:如果迭代一个`jobs`数组，通常希望在局部视图中透过`job`变数存取每一个job。

传递第四个参数至`@each`指令。此参数为：当给定的数组为空时，将会被渲染的视图。

```
@each('view.name', $jobs, 'job', 'view.empty')
```

### 5.7.4.5 注释

Blade还允许在视图中定义注释，然而，不同于HTML注释，Blade注释并不会包含到HTML中被返回：

```
{{-- This comment will not be present in the rendered HTML --}}
```

--------------------------------------------------------------------------------

## 5.7.5 服务注入

`@inject`指令可以用于从服务容器中获取服务，传递给`@inject`的第一个参数是服务将要被放置的变量名，第二个参数是要解析的服务类名或接口名：

```php
@inject('metrics', 'App\Services\MetricsService')

<div>
    Monthly Revenue: {{ $metrics->monthlyRevenue() }}.
</div>
```

--------------------------------------------------------------------------------

## 5.7.6 扩展 Blade

Blade还允许你自定义指令，可以使用`directive`方法来注册一个指令。当Blade编辑器遇到该指令，将会传入参数并调用提供的回调。

下面的例子创建了一个`@datetime($var)`指令,格式化给定的`$var`:

```php
<?php

namespace App\Providers;

use Blade;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider
{
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        Blade::directive('datetime', function($expression) {
            return "<?php echo with{$expression}->format('m/d/Y H:i'); ?>";
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

laravel 辅助函数`with`被用在该指令中，`with`方法返回给定的对象或值，允许方法链。最终该指令生成的PHP代码如下：

```php
<?php echo with($var)->format('m/d/Y H:i'); ?>
```
