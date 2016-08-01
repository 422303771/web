## 9.13 辅助函数

### 9.13.1 简介

Laravel 自带了一系列`PHP`辅助函数，很多被框架自身使用，如果你觉得方便的话也可以在代码中使用它们。

------

### 9.13.2 数组函数

* **array_add()**

	`array_add`函数添加给定键值对到数组，如果给定键不存在的话：
	
	```php
	$array = array_add(['name' => 'Desk'], 'price', 100);
	// ['name' => 'Desk', 'price' => 100]
	```

* **array_collapse()**

	`array_collapse`函数将多个数组合并成一个：
	
	```php
	$array = array_collapse([[1, 2, 3], [4, 5, 6], [7, 8, 9]]);
	// [1, 2, 3, 4, 5, 6, 7, 8, 9]
	```

* **array_divide()**

	`array_divide`函数返回两个数组，一个包含原数组的所有键，另外一个包含原数组的所有值：
	
	```php
	list($keys, $values) = array_divide(['name' => 'Desk']);
	// $keys: ['name']
	// $values: ['Desk']
	```

* **array_dot()**

	`array_dot`函数使用`.`号将将多维数组转化为一维数组：
	
	```php
	$array = array_dot(['foo' => ['bar' => 'baz']]);
	// ['foo.bar' => 'baz'];
	```

* **array_except()**

	`array_except`方法从数组中移除给定键值对：
	
	```php
	$array = ['name' => 'Desk', 'price' => 100];
	
	$array = array_except($array, ['price']);
	// ['name' => 'Desk']
	```

* **array_first()**

	`array_first`方法返回通过测试数组的第一个元素：
	
	```php
	$array = [100, 200, 300];
	
	$value = array_first($array, function ($key, $value) {
	    return $value >= 150;});
	// 200
	```
	默认值可以作为第三个参数传递给该方法，如果没有值通过测试的话返回默认值：

	```php
	$value = array_first($array, $callback, $default);
	```
* **array_flatten()**

	`array_flatten`方法将多维数组转化为一维数组：
	
	```php
	$array = ['name' => 'Joe', 'languages' => ['PHP', 'Ruby']];
	
	$array = array_flatten($array);
	// ['Joe', 'PHP', 'Ruby'];
	```

* **array_forget()**

	`array_forget`方法使用`.`号从嵌套数组中移除给定键值对：
	
	```php
	$array = ['products' => ['desk' => ['price' => 100]]];
	
	array_forget($array, 'products.desk');
	// ['products' => []]
	```

* **array_get()**

	`array_get`方法使用`.`号从嵌套数组中获取值：
	
	```php
	$array = ['products' => ['desk' => ['price' => 100]]];
	
	$value = array_get($array, 'products.desk');
	// ['price' => 100]
	```
	`array_get`函数还接收一个默认值，如果指定键不存在的话则返回该默认值：
	
	```php
	$value = array_get($array, 'names.john', 'default');
	```

* **array_only()**

	`array_only`方法只从给定数组中返回指定键值对：
	
	```php
	$array = ['name' => 'Desk', 'price' => 100, 'orders' => 10];
	
	$array = array_only($array, ['name', 'price']);
	// ['name' => 'Desk', 'price' => 100]
	```

* **array_pluck()**

	`array_pluck`方法从数组中返回给定键对应的键值对列表：
	
	```php
	$array = [
	    ['developer' => ['name' => 'Taylor']],
	    ['developer' => ['name' => 'Abigail']]];
	
	$array = array_pluck($array, 'developer.name');
	// ['Taylor', 'Abigail'];
	```
	你还可以指定返回结果的键：
	
	```php
	$array = array_pluck($array, 'developer.name', 'developer.id');
	// [1 => 'Taylor', 2 => 'Abigail'];
	```
	
* **array_prepend()**

	`array_prepend`函数将数据项推入数组开头：
	
	```php
	$array = ['one', 'two', 'three', 'four'];
	
	$array = array_prepend($array, 'zero');
	// $array: ['zero', 'one', 'two', 'three', 'four']
	```

* **array_pull()**
	
	`array_pull`方法从数组中返回并移除键值对：
	
	```php
	$array = ['name' => 'Desk', 'price' => 100];
	
	$name = array_pull($array, 'name');
	// $name: Desk
	
	// $array: ['price' => 100]
	```

* **array_set()**

	`array_set`方法在嵌套数组中使用`.`号设置值：
	
	```php
	$array = ['products' => ['desk' => ['price' => 100]]];
	
	array_set($array, 'products.desk.price', 200);
	// ['products' => ['desk' => ['price' => 200]]]
	```

* **array_sort()**

	`array_sort`方法通过给定闭包的结果对数组进行排序：
	
	```php
	$array = [
	    ['name' => 'Desk'],
	    ['name' => 'Chair'],
	];
	
	$array = array_values(array_sort($array, function ($value) {
	    return $value['name'];
	}));
	
	/*
	    [
	        ['name' => 'Chair'],
	        ['name' => 'Desk'],
	    ]
	*/
	```

* **array_sort_recursive()**
	
	`array_sort_recursive`函数使用`sort`函数对数组进行递归排序：
	
	```php
	$array = [
	    [
	        'Roman',
	        'Taylor',
	        'Li',
	    ],
	    [
	        'PHP',
	        'Ruby',
	        'JavaScript',
	    ],
	];
	
	$array = array_sort_recursive($array);
	
	/*
	    [
	        [
	            'Li',
	            'Roman',
	            'Taylor',
	        ],
	        [
	            'JavaScript',
	            'PHP',
	            'Ruby',
	        ]
	    ];
	*/
	```

* **array_where()**

	`array_where`函数使用给定闭包对数组进行过滤：
	
	```php
	$array = [100, '200', 300, '400', 500];
	
	$array = array_where($array, function ($key, $value) {
	    return is_string($value);
	});
	// [1 => 200, 3 => 400]
	```

* **head()**

	`head`函数只是简单返回给定数组的第一个元素：
	
	```php
	$array = [100, 200, 300];
	
	$first = head($array);
	// 100
	```
	
* **last()**

	`last`函数返回给定数组的最后一个元素：
	
	```php
	$array = [100, 200, 300];
	
	$last = last($array);
	// 300
	```


-----

### 9.13.3 路径函数

* **app_path()**
	
	`app_path`函数返回`app`目录的绝对路径：
	
	```php
	$path = app_path();
	```
	你还可以使用`app_path`函数为相对于`app`目录的给定文件生成绝对路径：
	
	```php
	$path = app_path('Http/Controllers/Controller.php');
	```

* **base_path()**

	`base_path`函数返回项目根目录的绝对路径：
	
	```php
	$path = base_path();
	```
	你还可以使用`base_path`函数为相对于应用目录的给定文件生成绝对路径：
	```php
	$path = base_path('vendor/bin');
	```

* **config_path()**

	`config_path`函数返回应用配置目录的绝对路径：
	
	```php
	$path = config_path();
	```

* **database_path()**

	`database_path`函数返回应用数据库目录的绝对路径：
	
	```php
	$path = database_path();
	```

* **elixir()**

	`elixir`函数返回版本控制的Elixir文件所在路径：
	
	```php
	elixir($file);
	```

* **public_path()**

	`public_path`函数返回`public`目录的绝对路径：
	
	```php
	$path = public_path();
	```

* **storage_path()**

	`storage_path`函数返回`storage`目录的绝对路径：
	
	```php
	$path = storage_path();
	```
	还可以使用`storage_path`函数生成相对于`storage`目录的给定文件的绝对路径：
	
	```php
	$path = storage_path('app/file.txt');
	```
	
-----

### 9.13.4 字符串函数

* **camel_case()**

	`camel_case`函数将给定字符串转化为按驼峰式命名规则的字符串：
	
	```php
	$camel = camel_case('foo_bar');
	// fooBar
	```
* **class_basename()**
	
	`class_basename`返回给定类移除命名空间后的类名：
	
	```php
	$class = class_basename('Foo\Bar\Baz');
	// Baz
	```
* **e()**

	`e`函数在给定字符串上运行`htmlentities`：
	
	```php
	echo e('<html>foo</html>');
	// &lt;html&gt;foo&lt;/html&gt;
	```

* **ends_with()**
	
	`ends_with`函数判断给定字符串是否以给定值结尾：
	
	```php
	$value = ends_with('This is my name', 'name');
	// true
	```

* **snake_case()**

	`snake_case`函数将给定字符串转化为下划线分隔的字符串：
	
	```php
	$snake = snake_case('fooBar');
	// foo_bar
	```

* **str_limit()**

	`str_limit`函数限制输出字符串的数目，该方法接收一个字符串作为第一个参数以及该字符串最大输出字符数作为第二个参数：
	
	```php
	$value = str_limit('The PHP framework for web artisans.', 7);
	// The PHP...
	```

* **starts_with()**

	`starts_with`函数判断给定字符串是否以给定值开头：
	
	```php
	$value = starts_with('This is my name', 'This');
	// true
	```

* **str_contains()**

	`str_contains`函数判断给定字符串是否包含给定值：
	
	```php
	$value = str_contains('This is my name', 'my');
	// true
	```

* **str_finish()**

	`str_finish`函数添加字符到字符串结尾：
	
	```php
	$string = str_finish('this/string', '/');
	// this/string/
	```
	
* **str_is()**

	`str_is`函数判断给定字符串是否与给定模式匹配，星号可用于表示通配符：

	```php
	$value = str_is('foo*', 'foobar');
	// true
	$value = str_is('baz*', 'foobar');
	// false
	```

* **str_plural()**

	`str_plural`函数将字符串转化为复数形式，该函数当前只支持英文：
	
	```php
	$plural = str_plural('car');
	// cars
	$plural = str_plural('child');
	// children
	```
	还可以传递整型数据作为第二个参数到该函数以获取字符串的单数或复数形式：
	
	```php
	$plural = str_plural('child', 2);
	// children
	$plural = str_plural('child', 1);
	// child
	```
	
* **str_random()**

	`str_random`函数通过指定长度生成随机字符串：
	
	```php
	$string = str_random(40);
	```
* **str_singular()**

	`str_singular`函数将字符串转化为单数形式，该函数目前只支持英文：
	
	```php
	$singular = str_singular('cars');
	// car
	```

* **str_slug()**

	`str_slug`函数将给定字符串生成URL友好的格式：
	
	```php
	$title = str_slug("Laravel 5 Framework", "-");
	// laravel-5-framework
	```

* **studly_case()**

	`studly_case`函数将给定字符串转化为单词开头字母大写的格式：
	
	```php
	$value = studly_case('foo_bar');
	// FooBar
	```

* **trans()**

	`trans`函数使用本地文件翻译给定语言行：
	
	```php
	echo trans('validation.required'):
	```
* **trans_choice()**

	`trans_choice`函数翻译带拐点的给定语言行：
	
	```php
	$value = trans_choice('foo.bar', $count);
	```


-----

### 9.13.5 URL函数

* **action()**
	
	`action`函数为给定控制器动作生成URL，你不需要传递完整的命名空间到该控制器，传递相对于命名空间`App\Http\Controllers`的类名即可：
	
	```php
	$url = action('HomeController@getIndex');
	```
	如果该方法接收路由参数，你可以将其作为第二个参数传递进来：
	
	```php
	$url = action('UserController@profile', ['id' => 1]);
	```
* **asset()**

	使用当前请求的`scheme`（HTTP或HTTPS）为前端资源生成一个URL：
	
	```php
	$url = asset('img/photo.jpg');
	```
* **secure_asset()**

	使用`HTTPS`为前端资源生成一个 URL：
	
	```php
	echo secure_asset('foo/bar.zip', $title, $attributes = []);
	```
* **route()**
	
	`route`函数为给定命名路由生成一个URL：
	
	```php
	$url = route('routeName');
	```
	如果该路由接收参数，你可以将其作为第二个参数传递进来：
	
	```php
	$url = route('routeName', ['id' => 1]);
	```
	
* **url()**

	`url`函数为给定路径生成绝对路径：
	
	```php
	echo url('user/profile');
	echo url('user/profile', [1]);
	```
	如果没有提供路径，将会返回 `Illuminate\Routing\UrlGenerator`实例：
	
	```php
	echo url()->current();
	echo url()->full();
	echo url()->previous();
	```
----

### 9.13.6 其它函数

* **auth()**

	`auth`函数返回一个认证器实例，为方便起见你可以用其取代 `Auth`门面：
	
	```php
	$user = auth()->user();
	```
* **back()**
	
	`back`函数生成重定向响应到用户前一个位置：
	
	```php
	return back();
	```
* **bcrypt()**

	`bcrypt`函数使用`Bcrypt`对给定值进行哈希，你可以用其替代`Hash`门面：
	
	```php
	$password = bcrypt('my-secret-password');
	```

* **collect()**

	`collect`函数会根据提供的数据项创建一个集合：
	
	```php
	$collection = collect(['taylor', 'abigail']);
	```
* **config()**

	`config`函数获取配置变量的值，配置值可以通过使用`.`号访问，包含文件名以及你想要访问的选项。如果配置选项不存在的话默认值将会被指定并返回：
	
	```php
	$value = config('app.timezone');
	
	$value = config('app.timezone', $default);
	```
	辅助函数`config`还可以用于在运行时通过传递键值对数组设置配置变量值：
	
	```php
	config(['app.debug' => true]);
	```
* **csrf_field()**

	`csrf_field`函数生成一个包含`CSRF`令牌值的`HTML` 隐藏域。
	
	例如，使用Blade语法：
	
	```php
	{!! csrf_field() !!}
	```

* **csrf_token()**

	`csrf_token`函数获取当前 CSRF 令牌的值：
	
	```php
	$token = csrf_token();
	```
* **dd()**

	`dd`函数输出给定变量值并终止脚本执行：
	
	```php
	dd($value);
	```
* **dispatch()**
	
	`dispatch`函数推送一个新的任务到Laravel任务队列：
	
	```php
	dispatch(new App\Jobs\SendEmails);
	```
* **env()**

	`env`函数获取环境变量值或返回默认值：
	
	```php
	$env = env('APP_ENV');
	// Return a default value if the variable doesn't exist...
	$env = env('APP_ENV', 'production');
	```

* **event()**

	`event`函数分发给定事件到对应监听器：
	
	```php
	event(new UserRegistered($user));
	```
* **factory()**

	`factory`函数为给定类、名称和数量创建模型工厂构建器，可用于测试或数据填充：
	
	```php
	$user = factory('App\User')->make();
	```
* **method_field()**

	`method_field`函数生成包含HTTP请求方法的HTML隐藏域。例如：
	
	```php
	<form method="POST">
	    {!! method_field('delete') !!}</form>
	```

* **old()**

	`old`函数获取一次性存放在 Session 中的值：
	
	```php
	$value = old('value');
	$value = old('value', 'default');
	```
* **redirect()**

	`redirect`函数返回重定向器实例进行重定向：
	
	```php
	return redirect('/home');
	```
* **request()**

	`request`函数返回当前请求实例或者获取一个输入项：
	
	```php
	$request = request();$value = request('key', $default = null)
	```
* **response()**

	`response`函数创建一个响应实例或者获取响应工厂实例：
	
	```php
	return response('Hello World', 200, $headers);
	return response()->json(['foo' => 'bar'], 200, $headers)
	```
* **session()**

	`session`函数可以用于获取/设置`Session`值：
	
	```php
	$value = session('key');
	```
	可以通过传递键值对数组到该函数的方式设置`Session`值：
	```php
	session(['chairs' => 7, 'instruments' => 3]);
	```
	如果没有传入参数到`session`函数则返回`Session`存储器对象实例：
	
	```php
	$value = session()->get('key');session()->put('key', $value);
	```
	
* **value()**

	`value`函数返回给定的值，然而，如果你传递一个闭包到该函数，该闭包将会被执行并返回执行结果：
	
	```php
	$value = value(function() { return 'bar'; });
	```

* **view()**

	`view`函数获取一个视图实例：
	
	```php
	return view('auth.login');
	```
* **with()**

	`with`函数返回给定的值，该函数在方法链中特别有用，别的地方就没什么用了：
	
	```php
	$value = with(new Foo)->work();
	```

-----


## 9.14 本地化

### 9.14.1 简介

Laravel的本地化特性允许你在应用中轻松实现多种语言支持。

语言字符串默认存放在`resources/lang`目录中，在该目录中应该包含应用支持的每种语言的子目录：

```php
/resources
    /lang
        /en
            messages.php
        /es
            messages.php
```
所有语言文件都返回一个键值对数组，例如：

```php
<?php

return [
    'welcome' => 'Welcome to our application'
];
```

* **配置Locale选项**

	应用默认语言存放在配置文件`config/app.php`中，当然，你可以修改该值来匹配应用需要。你还可以在运行时使用`App`门面上的`setLocale`方法改变当前语言：
	
	```php
	Route::get('welcome/{locale}', function ($locale) {
	    App::setLocale($locale);
	    //
	});
	```
	你还可以配置一个`备用语言`，当当前语言不包含给定语言行时备用语言被返回。和默认语言一样，备用语言也在配置文件`config/app.php`中配置：
	
	```php
	'fallback_locale' => 'en',
	```



-----

### 9.14.2 基本使用

你可以使用帮助函数`trans`从语言文件中获取行，该方法接收文件和语言行的键作为第一个参数。

例如，让我们在语言文件`resources/lang/messages.php`中获取语言行`welcome`：

```php
echo trans('messages.welcome');
```
当然如果你使用Blade模板引擎，可以使用`{{ }}`语法打印语言行：
```php
{{ trans('messages.welcome') }}
```
如果指定的语言行不存在，`trans`函数将返回语言行的键，所以，使用上面的例子，如果语言行不存在的话，`trans`函数将返回`messages.welcome`。

* **替换语言行中的参数**

	如果需要的话，你可以在语言行中定义占位符，所有的占位符都有一个`:`前缀。
	
	例如，你可以用占位符名称定义一个`welcome`消息：
	
	```php
	'welcome' => 'Welcome, :name',
	```
	要在获取语言行的时候替换占位符，传递一个替换数组作为`trans`函数的第二个参数：
	
	```php
	echo trans('messages.welcome', ['name' => 'Dayle']);
	```
#### 9.14.2.1 多元化

多元化是一个复杂的问题，因为不同语言对多元化有不同的规则，通过使用管道字符`|`，你可以区分一个字符串的单数和复数形式：

```php
'apples' => 'There is one apple|There are many apples',
```
然后，你可以使用`trans_choice`函数获取给定行数的语言行，在本例中，由于行数大于`1`，将会返回语言行的复数形式：

```php
echo trans_choice('messages.apples', 10);
```
Laravel 翻译器由`Symfony`翻译组件提供，因此你可以创建更复杂的多元化规则：

```php
'apples' => '{0} There are none|[1,19] There are some|[20,Inf] There are many',
```

-----

### 9.14.3 覆盖Vendor包的语言文件

有些包可以处理自己的语言文件。你可以通过将自己的文件放在`resources/lang/vendor/{package}/{locale}`目录下来覆盖它们而不是破坏这些包的核心文件来调整这些句子。

所以，举个例子，如果你需要覆盖名为`skyrim/hearthfire`的包中的`messages.php`文件里的英文句子，可以创建一个`resources/lang/vendor/hearthfire/en/messages.php`文件。在这个文件中只需要定义你想要覆盖的句子，你没有覆盖的句子仍然从该包原来的语言文件中加载。


----