## Eloquent ORM

## 8.1 起步

### 8.1.1 简介

Laravel 自带的 `Eloquent ORM `提供了一个美观、简单的与数据库打交道的 `ActiveRecord` 实现方法，每张数据表都对应一个与该表进行交互的“模型”，模型允许你在表中进行数据查询，以及插入、更新、删除等操作。

在开始之前，确保在config/database.php文件中配置好了数据库连接。更多关于数据库配置的信息，请查看文档。

----

### 8.1.2 定义模型

作为开始，让我们创建一个 `Eloquent` 模型，模型通常位于`app目录`下，你也可以将其放在其他可以被`composer.json`文件自动加载的地方。所有Eloquent模型都继承自 `Illuminate\Database\Eloquent\Model`类。

创建模型实例最简单的办法就是使用 Artisan 命令`make:model`：

	php artisan make:model User

如果你想要在生成模型时生成数据库迁移，可以使用`--migration`或`-m`选项：

	php artisan make:model User --migration
	php artisan make:model User -m

#### 8.1.2.1 Eloquent 模型约定

现在，让我们来看一个 `Flight `模型类例子，我们将用该类获取和存取数据表`flights`中的信息：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model{
    //
}
```

* **表名**
	
	注意我们并没有告诉 `Eloquent` 我们的`Flight`模型使用哪张表。默认规则是模型类名的复数作为与其对应的表名，除非在模型类中明确指定了其它名称。
	
	所以，在本例中，Eloquent 认为`Flight`模型存储记录在`flights`表中。你也可以在模型中定义table属性来指定自定义的表名：
	
	下面例子中表明被定义为`my_flights`
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Flight extends Model{
	    /**
	     * 关联到模型的数据表
	     *
	     * @var string
	     */
	    protected $table = 'my_flights';
	}
	```
	
* **主键**

	Eloquent 默认每张表的主键名为`id`，你可以在模型类中定义一个`$primaryKey`属性来覆盖该设置。

* **时间戳**

	默认情况下，Eloquent 期望`created_at`和`updated_at`已经存在于数据表中，如果你不想要这些 Laravel 自动管理的列，在模型类中设置`$timestamps`属性为`false`：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Flight extends Model{
	    /**
	     * 表明模型是否应该被打上时间戳
	     *
	     * @var bool
	     */
	    public $timestamps = false;
	}
	```
	如果你需要自定义时间戳格式，设置模型中的`$dateFormat`属性。该属性决定日期被如何存储到数据库中，以及模型被序列化为数组或 JSON 时日期的格式：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Flight extends Model{
	    /**
	     * 模型日期列的存储格式
	     *
	     * @var string
	     */
	    protected $dateFormat = 'U';
	}
	```

* **数据库连接**

	默认情况下，所有的 Eloquent 模型使用应用配置中的默认数据库连接，如果你想要为模型指定不同的连接，可以通过 `$connection`属性来设置。

	这里的`Flight`模型，默认连接到`Flights`。设置后将`Flight`模型连接到`connection_name`，这里的`connection_name`可以随意命名。

	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Flight extends Model{
	    /**
	     * The connection name for the model.
	     *
	     * @var string
	     */
	    protected $connection = 'connection_name';
	}
	```
----

### 8.1.3 获取多个模型

创建完模型及其关联的数据表后，就要准备从数据库中获取数据。将Eloquent模型看作功能强大的查询构建器，你可以使用它来流畅的查询与其关联的数据表。

例如：例子中将`Flights`表中全部数据，注入到`flight.index`页面中。


```php
<?php

namespace App\Http\Controllers;

use App\Flight;
use App\Http\Controllers\Controller;

class FlightController extends Controller{
    /**
     * 显示所有有效航班列表
     *
     * @return Response
     */
    public function index()
    {
        $flights = Flight::all();
        return view('flight.index', ['flights' => $flights]);
    }
}
```

#### 8.1.3.1 访问列值

如果你有一个 Eloquent 模型实例，可以通过访问其相应的属性来访问模型的列值。

例如，让我们循环查询返回的每一个`Flight`实例并输出`name`的值：

```php
foreach ($flights as $flight) {
    echo $flight->name;
}
```

#### 8.1.3.2 添加额外约束

Eloquent 的`all`方法返回模型表的所有结果，由于每一个Eloquent模型都是一个查询构建器，你还可以添加约束条件到查询，然后使用get方法获取对应结果：

```php
$flights = App\Flight::where('active', 1)
               ->orderBy('name', 'desc')
               ->take(10)
               ->get();
```

*注意：由于 Eloquent 模型本质上就是查询构建器，你可以在Eloquent查询中使用查询构建器的所有方法*

#### 8.1.3.3 集合

对 Eloquent 中获取多个结果的方法（比如`all`和`get`）而言，其返回值是`Illuminate\Database\Eloquent\Collection`的一个实例，`Collection`类提供了多个有用的函数来处理Eloquent结果。

当然，你可以像操作数组一样简单循环这个集合：

```php
foreach ($flights as $flight) {
    echo $flight->name;
}
```

#### 8.1.3.4 组块结果集

如果你需要处理成千上万个 Eloquent 结果，可以使用`chunk`命令。`chunk`方法会获取一个“组块”的 Eloquent 模型，并将其填充到给定闭包进行处理。使用`chunk`方法能够在处理大量数据集合时有效减少内存消耗：

```php
Flight::chunk(200, function ($flights) {
    foreach ($flights as $flight) {
        //
    }
});
```

传递给该方法的第一个参数是你想要获取的`组块`数目，闭包作为第二个参数被调用用于处理每个从数据库获取的区块数据。

----

### 8.1.4 获取单个模型/聚合

当然，除了从给定表中获取所有记录之外，还可以使用`find`和`first`获取单个记录。这些方法返回单个模型实例而不是返回模型集合：

```php
// 通过主键获取模型...
$flight = App\Flight::find(1);
// 获取匹配查询条件的第一个模型...
$flight = App\Flight::where('active', 1)->first();
```
* **Not Found 异常**

有时候你可能想要在模型找不到的时候抛出异常，这在路由或控制器中非常有用，`findOrFail`和`firstOrFail`方法会获取查询到的第一个结果。

然而，如果没有任何查询结果，`Illuminate\Database\Eloquent\ModelNotFoundException`异常将会被抛出：
```php
$model = App\Flight::findOrFail(1);
$model = App\Flight::where('legs', '>', 100)->firstOrFail();
```
如果异常没有被捕获，那么HTTP 404 响应将会被发送给用户，所以在使用这些方法的时候没有必要对返回404响应编写明确的检查：

```php
Route::get('/api/flights/{id}', function ($id) {
    return App\Flight::findOrFail($id);
});
```
#### 8.1.4.1 获取聚合

当然，你还可以使用查询构建器聚合方法，例如`count`、`sum`、`max`，以及其它查询构建器提供的聚合方法。这些方法返回计算后的结果而不是整个模型实例：

```php
$count = App\Flight::where('active', 1)->count();
$max = App\Flight::where('active', 1)->max('price');
```

----

### 8.1.5 插入/更新模型

#### 8.1.5.1 基本插入

想要在数据库中插入新的记录，只需创建一个新的模型实例，设置模型的属性，然后调用`save`方法：

```php
<?php

namespace App\Http\Controllers;

use App\Flight;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class FlightController extends Controller{
    /**
     * 创建一个新的航班实例
     *
     * @param  Request  $request
     * @return Response
     */
    public function store(Request $request)
    {
        // Validate the request...

        $flight = new Flight;

        $flight->name = $request->name;

        $flight->save();
    }
}
```
在这个例子中，我们只是简单分配HTTP请求中的`name`参数值给`App\Flight`模型实例的那么属性，当我们调用`save`方法时，一条记录将会被插入数据库。`created_at`和`updated_at`时间戳在`save`方法被调用时会自动被设置，所以没必要手动设置它们。

#### 8.1.5.2 基本更新

`save`方法还可以用于更新数据库中已存在的模型。要更新一个模型，应该先获取它，设置你想要更新的属性，然后调用`save`方法。同样，`updated_at`时间戳会被自动更新，所以没必要手动设置其值：

```php
$flight = App\Flight::find(1);
$flight->name = 'New Flight Name';
$flight->save();
```
更新操作还可以同时修改给定查询提供的多个模型实例，在本例中，所有有效且`destination=San Diego`的航班都被标记为延迟：

```php
App\Flight::where('active', 1)
          ->where('destination', 'San Diego')
          ->update(['delayed' => 1]);
```
`update`方法要求以数组形式传递键值对参数，代表着数据表中应该被更新的列。

#### 8.1.5.3 批量赋值

还可以使用`create`方法保存一个新的模型。该方法返回被插入的模型实例。

但是，在此之前，你需要指定模型的`fillable`或`guarded`属性，因为所有Eloquent模型都通过批量赋值（Mass Assignment）进行保护。

当用户通过 HTTP 请求传递一个不被期望的参数值时就会出现安全隐患，然后该参数以不被期望的方式修改数据库中的列值。

例如，恶意用户通过 HTTP 请求发送一个`is_admin`参数，然后该参数映射到模型的create方法，从而允许用户将自己变成管理员。

所以，你应该在模型中定义哪些属性是可以进行赋值的，使用模型上的`$fillable`属性即可实现。

例如，我们设置`Flight`模型上的`name`属性可以被赋值：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model{
    /**
     * 可以被批量赋值的属性.
     *
     * @var array
     */
    protected $fillable = ['name'];
}
```
设置完可以被赋值的属性之后，我们就可以使用`create`方法在数据库中插入一条新的记录。`create`方法返回保存后的模型实例：

	$flight = App\Flight::create(['name' => 'Flight 10']);

`$fillable`就像是可以被赋值属性的“白名单”，还可以选择使用`$guarded`。`$guarded`属性包含你不想被赋值的属性数组。所以不被包含在其中的属性都是可以被赋值的，因此，`$guarded`方法就像“黑名单”。

当然，你只能同时使用其中一个——而不是一起使用：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model{
    /**
     * 不能被批量赋值的属性
     *
     * @var array
     */
    protected $guarded = ['price'];
}
```
在这个例子中，除了`$price`之外的所有属性都是可以被赋值的。

* **其它创建方法**

	还有其它两种可以用来创建模型的方法：`firstOrCreate`和`firstOrNew`。
	
	`firstOrCreate`方法先尝试通过给定列/值对在数据库中查找记录，如果没有找到的话则通过给定属性创建一个新的记录。
	
	`firstOrNew`方法和`firstOrCreate`方法一样先尝试在数据库中查找匹配的记录，如果没有找到，则返回一个的模型实例。
	
	注意通过`firstOrNew`方法返回的模型实例并没有持久化到数据库中，你还需要调用`save`方法手动持久化：
	
	```php
	// 通过属性获取航班, 如果不存在则创建...
	$flight = App\Flight::firstOrCreate(['name' => 'Flight 10']);
	// 通过属性获取航班, 如果不存在初始化一个新的实例...
	$flight = App\Flight::firstOrNew(['name' => 'Flight 10']);
	```
-----

### 8.1.6 删除模型

要删除一个模型，调用模型实例上的`delete`方法：

```
$flight = App\Flight::find(1);
$flight->delete();
```

#### 8.1.6.1 通过主键删除模型

在上面的例子中，我们在调用`delete`方法之前从数据库中获取该模型，然而，如果你知道模型的主键的话，可以调用`destroy`方法直接删除而不需要获取它：

下方三种方式相同。

```
App\Flight::destroy(1);
App\Flight::destroy([1, 2, 3]);
App\Flight::destroy(1, 2, 3);
```

#### 8.1.6.2 通过查询删除模型

当然，你还可以通过查询删除多个模型，在本例中，我们删除所有被标记为无效的航班：

```
$deletedRows = App\Flight::where('active', 0)->delete();
```

#### 8.1.6.3 软删除

除了从数据库删除记录外，Eloquent还可以对模型进行“软删除”。当模型被软删除后，它们并没有真的从数据库删除，而是在模型上设置一个`deleted_at`属性并插入数据库，如果模型有一个非空`deleted_at`值，那么该模型已经被软删除了。

要启用模型的软删除功能，可以使用模型上的`Illuminate\Database\Eloquent\SoftDeletes`方法并添加`deleted_at`列到`$dates`属性：

```php

<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\SoftDeletes;

class Flight extends Model{
    use SoftDeletes;

    /**
     * 应该被调整为日期的属性
     *
     * @var array
     */
    protected $dates = ['deleted_at'];
}
```
当然，应该添加`deleted_at`列到数据表。Laravel Schema构建器包含一个帮助函数来创建该列：

```php
Schema::table('flights', function ($table) {
    $table->softDeletes();
});
```
现在，当调用模型的`delete`方法时，`deleted_at`列将被设置为当前日期和时间，并且，当查询一个使用软删除的模型时，被软删除的模型将会自动从查询结果中排除。

判断给定模型实例是否被软删除，可以使用`trashed`方法：

```php
if ($flight->trashed()) {
    //
}
```

#### 8.1.6.4 查询被软删除的模型

* **包含软删除模型**

	正如上面提到的，软删除模型将会自动从查询结果中排除，但是，如果你想要软删除模型出现在查询结果中，可以使用`withTrashed`方法：
	
	```php
	$flights = App\Flight::withTrashed()
	                ->where('account_id', 1)
	                ->get();
	```
	`withTrashed`方法也可以用于关联查询中：
	
	```php
	$flight->history()->withTrashed()->get();
	```

* **只获取软删除模型**

	`onlyTrashed`方法之获取软删除模型：
	
	```php
	$flights = App\Flight::onlyTrashed()
	                ->where('airline_id', 1)
	                ->get();
	```

* **恢复软删除模型**

	有时候你希望恢复一个被软删除的模型，可以使用`restore`方法：
	
		$flight->restore();
	
	你还可以在查询中使用`restore`方法来快速恢复多个模型：
	
	```php
	App\Flight::withTrashed()
	        ->where('airline_id', 1)
	        ->restore();
	```
	和`withTrashed`方法一样，`restore`方法也可以用于关联查询：
	
		$flight->history()->restore();

* **永久删除模型**

	有时候你真的需要从数据库中删除一个模型，可以使用forceDelete方法：
	
	```php
	// 强制删除单个模型实例...
	$flight->forceDelete();
	// 强制删除所有关联模型...
	$flight->history()->forceDelete();
	```
-----

### 8.1.7 查询作用域

#### 8.1.7.1 全局作用域

全局作用域允许我们为给定模型的所有查询添加条件约束。Laravel 自带的软删除功能就使用了全局作用域来从数据库中拉出所有没有被删除的模型。编写自定义的全局作用域可以提供一种方便的、简单的方式来确保给定模型的每个查询都有特定的条件约束。

* **编写全局作用域**

	自定义全局作用域很简单，首先定义一个实现 `Illuminate\Database\Eloquent\Scope` 接口的类，该接口要求你实现一个方法：`apply`。需要的话可以在 `apply` 方法中添加 `where` 条件到查询：
	
	```php
	<?php
	
	namespace App\Scopes;
	
	use Illuminate\Database\Eloquent\Scope;
	use Illuminate\Database\Eloquent\Model;
	use Illuminate\Database\Eloquent\Builder;
	
	class AgeScope implements Scope{
	    /**
	     * Apply the scope to a given Eloquent query builder.
	     *
	     * @param  \Illuminate\Database\Eloquent\Builder  $builder
	     * @param  \Illuminate\Database\Eloquent\Model  $model
	     * @return void
	     */
	    public function apply(Builder $builder, Model $model)
	    {
	        return $builder->where('age', '>', 200);
	    }
	}
	```

* **应用全局作用域**

	要将全局作用域分配给模型，需要重写给定模型的 `boot` 方法并使用 `addGlobalScope` 方法：
	
	```php
	<?php
	
	namespace App;
	
	use App\Scopes\AgeScope;
	use Illuminate\Database\Eloquent\Model;
	
	class User extends Model{
	    /**
	     * The "booting" method of the model.
	     *
	     * @return void
	     */
	    protected static function boot()
	    {
	        parent::boot();
	
	        static::addGlobalScope(new AgeScope);
	    }
	}
	```
	添加作用域后，如果使用 `User::all()` 查询则会生成如下SQL语句：
	
		select * from `users` where `age` > 200

* **匿名的全局作用域**

Eloquent还允许我们使用闭包定义全局作用域，这在实现简单作用域的时候特别有用，这样的话，我们就没必要定义一个单独的类了：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;
use Illuminate\Database\Eloquent\Builder;

class User extends Model{
    /**
     * The "booting" method of the model.
     *
     * @return void
     */
    protected static function boot()
    {
        parent::boot();

        static::addGlobalScope('age', function(Builder $builder) {
            $builder->where('age', '>', 200);
        });
    }
}
```
我们还可以通过以下方式移除全局作用：

```php
User::withoutGlobalScope('age')->get();
```

* **移除全局作用域**

如果想要在给定查询中移除指定全局作用域，可以使用 `withoutGlobalScope`：

```php
User::withoutGlobalScope(AgeScope::class)->get();
```
如果你想要移除某几个或全部全局作用域，可以使用 `withoutGlobalScopes` 方法：

```php
User::withoutGlobalScopes()->get();
User::withoutGlobalScopes([FirstScope::class, SecondScope::class])->get();
```


#### 8.1.7.2 本地作用域

本地作用域允许我们定义通用的约束集合以便在应用中复用。

例如，你可能经常需要获取最受欢迎的用户，要定义这样的一个作用域，只需简单在对应Eloquent模型方法前加上一个`scope`前缀，作用域总是返回查询构建器：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 只包含活跃用户的查询作用域
     *
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopePopular($query)
    {
        return $query->where('votes', '>', 100);
    }

    /**
     * 只包含激活用户的查询作用域
     *
     * @return \Illuminate\Database\Eloquent\Builder
     */
    public function scopeActive($query)
    {
        return $query->where('active', 1);
    }
}
```

* **使用查询作用域**
	
	作用域被定义好了之后，就可以在查询模型的时候调用作用域方法，但调用时不需要加上`scope`前缀，你甚至可以在同时调用多个作用域，例如：
	
		$users = App\User::popular()->active()->orderBy('created_at')->get();

* **动态作用域**

	有时候你可能想要定义一个可以接收参数的作用域，你只需要将额外的参数添加到你的作用域即可。作用域参数应该被定义在`$query`参数之后：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class User extends Model{
	    /**
	     * 只包含给用类型用户的查询作用域
	     *
	     * @return \Illuminate\Database\Eloquent\Builder
	     */
	    public function scopeOfType($query, $type)
	    {
	        return $query->where('type', $type);
	    }
	}
	```
	现在，你可以在调用作用域时传递参数了：
	
		$users = App\User::ofType('admin')->get();

-----

### 8.1.8 事件

Eloquent模型可以触发事件，允许你在模型生命周期中的多个时间点调用如下这些方法：`creating`, `created`, `updating`, `updated`, `saving`, `saved`,`deleting`, `deleted`, `restoring`, `restored`。事件允许你在一个指定模型类每次保存或更新的时候执行代码。

#### 8.1.8.1 基本使用

一个新模型被首次保存的时候，`creating`和`created`事件会被触发。

如果一个模型已经在数据库中存在并调用`save`方法，`updating / updated`事件会被触发，无论是创建还是更新，`saving / saved`事件都会被调用。

举个例子，我们在服务提供者中定义一个`Eloquent事件监听器`，在事件监听器中，我们会调用给定模型的`isValid`方法，如果模型无效会返回`false`。如果从`Eloquent事件监听器`中返回`false`则取消`save/update`操作：

```php
<?php

namespace App\Providers;

use App\User;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider{
    /**
     * 启动所有应用服务
     *
     * @return void
     */
    public function boot()
    {
        User::creating(function ($user) {
            if ( ! $user->isValid()) {
                return false;
            }
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