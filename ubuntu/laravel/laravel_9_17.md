## 9.17 分页

### 9.17.1 简介

在其他框架中，分页是件非常痛苦的事，`Laravel`则使其变得轻而易举。`Laravel`能够基于当前页智能生成一定范围的链接，且生成的`HTML`兼容`Bootstrap CSS`框架。 

-----

### 9.17.2 基本使用

#### 9.17.2.1 基于查询构建器分页

有多种方式实现分页，最简单的方式就是使用`查询构建器`或`Eloquent`模型的`paginate`方法。该方法基于当前用户查看页自动设置合适的偏移`offset`和限制`limit`。默认情况下，当前页通过`HTTP`请求查询字符串参数`?page`的值判断。当然，该值由Laravel自动检测，然后自动插入分页器生成的链接中。

让我们先来看看如何在查询上调用`paginate`方法。

在本例中，传递给`paginate`的唯一参数就是你每页想要显示的数目，这里我们指定每页显示15个：

```php
<?php

namespace App\Http\Controllers;

use DB;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 显示应用中的所有用户
     *
     * @return Response
     */
    public function index()
    {
        $users = DB::table('users')->paginate(15);
        return view('user.index', ['users' => $users]);
    }
}
```
*注意：目前，使用`groupBy`的分页操作不能被Laravel有效执行，如果你需要在分页结果中使用`groupBy`，推荐你手动查询数据库然后创建分页器。*

* **简单分页**

	如果你只需要在分页视图中简单的显示`下一个`和`上一个`链接，可以使用`simplePaginate`方法来执行该查询。在渲染包含大数据集的视图且不需要显示每个页码时非常有用：
	
	```php
	$users = DB::table('users')->simplePaginate(15);
	```

#### 9.17.2.2 基于Eloquent模型分页

你还可以对`Eloquent`查询结果进行分页，在本例中，我们对`User`模型进行分页，每页显示15条记录。

正如你所看到的，该语法和基于查询构建器的分页差不多：

```php
$users = App\User::paginate(15);
```
当然，你可以在设置其它约束调价之后调用`paginate`，比如`where`子句：

```php
$users = User::where('votes', '>', 100)->paginate(15);
```
你也可以使用`simplePaginate`方法：

```php
$users = User::where('votes', '>', 100)->simplePaginate(15);
```

#### 9.17.2.3 手动创建分页器

有时候你可能想要通过传递数组数据来手动创建分页实例，你可以基于自己的需求通过创建`Illuminate\Pagination\Paginator`或`Illuminate\Pagination\LengthAwarePaginator`实例来实现。

`Paginator`类不需要知道结果集中数据项的总数；然而，正因如此，该类也没有提供获取最后一页索引的方法。

`LengthAwarePaginator`接收参数和`Paginator`几乎一样，只是，它要求传入结果集的总数。

换句话说，`Paginator`对应`simplePaginat`e方法，而`LengthAwarePaginator`对应`paginate`方法。

当手动创建分页器实例的时候，应该手动对传递到分页器的结果集进行`切片`，如果你不确定怎么做，查看PHP函数`array_slice`。


-----

### 9.17.3 在视图中显示分页结果

当你调用`查询构建器`或`Eloquent`查询上的`paginate`或`simplePaginate`方法时，你将会获取一个分页器实例。

当调用`paginate`方法时，你将获取`Illuminate\Pagination\LengthAwarePaginator`，而调用方法`simplePaginate`时，将会获取`Illuminate\Pagination\Paginator`实例。

这些对象提供相关方法描述这些结果集，除了这些帮助函数外，分页器实例本身就是迭代器，可以像数组一样对其进行循环调用。

所以，获取到结果后，可以按如下方式使用`Blade`显示这些结果并渲染页面链接：

```php
<div class="container">
    @foreach ($users as $user)
        {{ $user->name }}
    @endforeach
</div>

{!! $users->links() !!}
```
`links`方法将会将结果集中的其它页面链接渲染出来。每个链接已经包含了`?page`查询字符串变量。记住，`render`方法生成的HTML兼容`Bootstrap CS`框架。

*注意：我们从`Blade`模板调用`render`方法时，确保使用`{!! !!}`语法以便HTML链接不被过滤。*

* **自定义分页链接**

	`setPath`方法允许你生成分页链接时自定义分页器使用的URI。
	
	例如，如果你想要分页器生成形如`http://example.com/custom/url?page=N`的链接，应该传递`custom/url`到`setPath`方法：
	
	```php
	Route::get('users', function () {
	    $users = App\User::paginate(15);
	    $users->setPath('custom/url');
	    //
	});
	```

* **添加参数到分页链接**

	你可以使用`appends`方法添加查询参数到分页链接查询字符串。
	
	例如，要添加`&sort=votes`到每个分页链接，应该像如下方式调用`appends`：
	
	```php
	{!! $users->appends(['sort' => 'votes'])->links() !!}
	```
	如果你想要添加`哈希片段`到分页链接，可以使用`fragment`方法。
	
	例如，要添加`#foo`到每个分页链接的末尾，像这样调用`fragment`方法：
	
	```php
	{!! $users->fragment('foo')->links() !!}
	```

* **更多辅助方法**

	你还可以通过如下分页器实例上的方法访问更多分页信息：
	
	* `$results->count()`
	* `$results->currentPage()`
	* `$results->firstItem()`
	* `$results->hasMorePages()`
	* `$results->lastItem()`
	* `$results->lastPage()` (使用simplePaginate时无效)
	* `$results->nextPageUrl()`
	* `$results->perPage()`
	* `$results->previousPageUrl()`
	* `$results->total()` (使用simplePaginate时无效)
	* `$results->url($page)`

------

### 9.17.4 将结果转化为JSON

Laravel分页器结果类实现了`Illuminate\Contracts\Support\JsonableInterface`契约并实现`toJson`方法，所以将分页结果转化为`JSON`非常简单。

你还可以简单通过从路由或控制器动作返回分页器实例将转其化为`JSON`：

```php
Route::get('users', function () {
    return App\User::paginate();
});
```
从分页器转化来的`JSON`包含了元信息如`total`, `current_page`,`last_page`等等，实际的结果对象数据可以通过该`JSON`数组中的`data`键访问。

下面是一个通过从路由返回的分页器实例创建的`JSON`例子：

```php
{
   "total": 50,
   "per_page": 15,
   "current_page": 1,
   "last_page": 4,
   "next_page_url": "http://laravel.app?page=2",
   "prev_page_url": null,
   "from": 1,
   "to": 15,
   "data":[
        {
            // Result Object
        },
        {
            // Result Object
        }
   ]
}
```

----

## 9.18 Redis

### 9.18.1 简介

`Redis`是一个开源的、高级的键值对存储系统，经常被用作数据结构服务器，因为其支持`字符串`、`Hash`、`列表`、`集合`和`有序集合`等数据结构。在Laravel中使用`Redis`之前，需要通过`Composer`安装`predis/predis`包（~1.0）。

#### 9.18.1.1 配置

应用的`Redis`配置位于配置文件`config/database.php`。在这个文件中，可以看到包含被应用使用的`Redis`服务器的`redis`数组：

```php
'redis' => [

    'cluster' => false,

    'default' => [
        'host'     => '127.0.0.1',
        'port'     => 6379,
        'database' => 0,
    ],

],
```
默认服务器配置可以满足开发需要，然而，你可以基于环境随意修改该数组，只需要给每个`Redis`服务器一个名字并指定该`Redis`服务器使用的主机和接口。

`cluster`选项告诉Laravel`Redis`客户端在多个`Redis`节点间执行客户端分片，从而形成节点池并创建大量有效的`RAM`。然而，客户端分片并不处理故障转移，所以，非常适合从另一个主数据存储那里获取有效的缓存数据。

此外，你可以在`Redis`连接定义中定义`options`数组值，从而允许你指定一系列`Predis`客户端选项。

*注意：如果你通过`PECL`安装`PHP`的`Redis`扩展，需要在`config/app.php`文件中修改`Redis`的别名。*

-----

### 9.18.2 基本使用

你可以通过调用`Redis`门面上的多个方法来与`Redis`进行交互，该门面支持动态方法，所以你可以任何`Redis`命令，该命令将会直接传递给`Redis`。

在本例中，我们通过调用`Redis`门面上的`get`方法来调用`Redis`上的`GET`命令：

```php
<?php

namespace App\Http\Controllers;

use Redis;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 显示指定用户属性
     *
     * @param  int  $id
     * @return Response
     */
    public function showProfile($id)
    {
        $user = Redis::get('user:profile:'.$id);
        return view('user.profile', ['user' => $user]);
    }
}
```
当然，如上所述，可以在`Redis`门面上调用任何`Redis`命令。Laravel使用魔术方法将命令传递给`Redis`服务器，所以只需简单传递参数和`Redis`命令如下：

```php
Redis::set('name', 'Taylor');
$values = Redis::lrange('names', 5, 10);
```
此外还可以使用`command`方法传递命令到服务器，该方法接收命令名作为第一个参数，参数值数组作为第二个参数：

```php
$values = Redis::command('lrange', ['name', 5, 10]);
```

* **使用多个Redis连接**

	你可以通过调用`Redis::connection`方法获取`Redis`实例：
	
	```php
	$redis = Redis::connection();
	```
	这将会获取默认`Redis`服务器实例，如果你没有使用服务器集群，可以传递服务器名到`connection`方法来获取指定`Redis`配置中定义的指定服务器：
	
	```php
	$redis = Redis::connection('other');
	```

#### 9.18.2.1 管道命令

当你需要在一次操作中发送多个命令到服务器的时候应该使用管道，`pipeline`方法接收一个参数：接收`Redis`实例的闭包。你可以将所有`Redis`命令发送到这个`Redis`实例，然后这些命令会在一次操作中被执行：

```php
Redis::pipeline(function ($pipe) {
    for ($i = 0; $i < 1000; $i++) {
        $pipe->set("key:$i", $i);
    }
});
```

-----

### 9.18.3 发布/订阅

`Redis`还提供了调用`Redis`的`publish`和`subscribe`命令的接口。这些`Redis`命令允许你在给定`频道`监听消息，你可以从另外一个应用发布消息到这个频道，甚至使用其它编程语言，从而允许你在不同的应用/进程之间轻松通信。

首先，让我们使用`subscribe`方法通过`Redis`在一个频道上设置监听器。由于调用`subscribe`方法会开启一个常驻进程，我们将在Artisan命令中调用该方法：

```php
<?php

namespace App\Console\Commands;

use Redis;
use Illuminate\Console\Command;

class RedisSubscribe extends Command{
    /**
     * 控制台命令名称
     *
     * @var string
     */
    protected $signature = 'redis:subscribe';

    /**
     * 控制台命令描述
     *
     * @var string
     */
    protected $description = 'Subscribe to a Redis channel';

    /**
     * 执行控制台命令
     *
     * @return mixed
     */
    public function handle()
    {
        Redis::subscribe(['test-channel'], function($message) {
            echo $message;
        });
    }
}
```
现在，我们可以使用`publish`发布消息到该频道：

```php
Route::get('publish', function () {
    // 路由逻辑...
    Redis::publish('test-channel', json_encode(['foo' => 'bar']));
});
```

* **通配符订阅**
	
	使用`psubscribe`方法，你可以订阅到一个通配符定义的频道，这在所有相应频道上获取所有消息时很有用。`$channel`名将会作为第二个参数传递给提供的回调闭包：
	
	```php
	Redis::psubscribe(['*'], function($message, $channel) {
	    echo $message;
	});
	
	Redis::psubscribe(['users.*'], function($message, $channel) {
	    echo $message;
	});
	```

-----