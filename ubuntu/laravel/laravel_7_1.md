# 7 数据库

# 7.1 起步

## 7.1.1 简介

Laravel 让连接多种数据库以及对数据库进行查询变得非常简单，不论使用原生 SQL、还是查询构建器，还是 Eloquent ORM。目前，Laravel 支持四种类型的数据库系统：

- MySQL
- Postgres
- SQLite
- SQL Server

### 7.1.1.1 配置

Laravel 让连接数据库和运行查询都变得非常简单。应用的数据库配置位于`config/database.php`。在该文件中你可以定义所有的数据库连接，并指定哪个连接是默认连接。该文件中提供了所有支持数据库系统的配置示例。

默认情况下，Laravel 示例环境配置已经为 Laravel Homestead 做好了设置，当然，你也可以按照需要为本地的数据库修改该配置。

- **读/写连接**

有时候你希望使用一个数据库连接做查询，另一个数据库连接做插入、更新和删除，Laravel 使得这件事情轻而易举，不管你用的是原生 SQL，还是查询构建器，还是 Eloquent ORM，合适的连接总是会被使用。

想要知道如何配置读/写连接，让我们看看下面这个例子：

```php
'mysql' => [
    'read' => [
        'host' => '192.168.1.1',
    ],
    'write' => [
        'host' => '196.168.1.2'
    ],
    'driver'    => 'mysql',
    'database'  => 'database',
    'username'  => 'root',
    'password'  => '',
    'charset'   => 'utf8',
    'collation' => 'utf8_unicode_ci',
    'prefix'    => '',
],
```

注意我们在配置数组中新增了两个键：read和write，这两个键都对应一个包含单个键"host"的数组，读/写连接的其它数据库配置选项都共用 mysql 的主数组配置。

如果我们想要覆盖主数组中的配置，只需要将相应配置项放到`read`和`write`数组中即可。在本例中，`192.168.1.1`将被用作"读"连接，而`192.168.1.2`将被用作"写"连接。两个数据库连接的凭证（用户名/密码）、前缀、字符集以及其它配置将会共享`mysql`数组中的设置。

--------------------------------------------------------------------------------

## 7.1.2 运行原生 SQL 查询

配置好数据库连接后，就可以使用`DB门`面来运行查询。`DB门面`为每种查询提供了相应方法：`select`, `update`, `insert`, `delete`, 和`statement`。

### 7.1.2.1 运行 Select 查询

运行一个最基本的查询，可以使用`DB门面`的`select`方法：

```php
<?php

namespace App\Http\Controllers;

use DB;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 显示用户列表
     *
     * @return Response
     */
    public function index()
    {
        $users = DB::select('select * from users where active = ?', [1]);
        return view('user.index', ['users' => $users]);
    }
}
```

传递给`select`方法的第一个参数是原生的`SQL`语句，第二个参数需要绑定到查询的参数绑定，通常，这些都是`where`字句约束中的值。参数绑定可以避免`SQL`注入攻击。

`select`方法以数组的形式返回结果，数组中的每一个结果都是一个PHP `StdClass`对象，从而允许你像下面这样访问结果值：

```php
foreach ($users as $user) {
    echo $user->name;
}
```

### 7.1.2.2 使用命名绑定

除了使用?占位符来代表参数绑定外，还可以使用命名绑定来执行查询:

```php
$results = DB::select('select * from users where id = :id', ['id' => 1]);
```

### 7.1.2.3 运行插入语句

使用`DB门面`的`insert`方法执行插入语句。和`select`一样，改方法将原生SQL语句作为第一个参数，将绑定作为第二个参数：

```php
$results = DB::insert('insert into users (id, name) values (?, ?)', [1, 'Dayle']);
```

### 7.1.2.4 运行更新语句

`update`方法用于更新数据库中已存在的记录，该方法返回受更新语句影响的行数：

```php
$affected = DB::update('update users set votes = 100 where name = ?', ['John']);
```

### 7.1.2.5 运行删除语句

`delete`方法用于删除数据库中已存在的记录，和`update`一样，该语句返回被删除的行数：

```php
$deleted = DB::delete('delete from users');
```

### 7.1.2.6 运行一个通用语句

有些数据库语句不返回任何值，对于这种类型的操作，可以使用`DB门面`的`statement`方法：

```php
DB::statement('drop table users');
```

### 7.1.2.7 监听查询事件

如果你想要获取应用中每次 SQL 语句的执行，可以使用`listen`方法，该方法对查询日志和调试非常有用，你可以在服务提供者中注册查询监听器：

```php
<?php

namespace App\Providers;

use DB;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider{
    /**
     * 启动所有应用服务
     *
     * @return void
     */
    public function boot()
    {
        DB::listen(function($query) {
            // $query->sql
            // $query->bindings
            // $query->time
        });
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

## 7.1.3 数据库事务

想要在一个数据库事务中运行一连串操作，可以使用`DB门面`的`transaction`方法，如果事务闭包中抛出异常，事务将会自动回滚。

如果闭包执行成功，事务将会自动提交。使用`transaction`方法时不需要担心手动回滚或提交：

```php
DB::transaction(function () {
    DB::table('users')->update(['votes' => 1]);
    DB::table('posts')->delete();
});
```

### 7.1.3.1 手动使用事务

如果你想要手动开始事务从而对回滚和提交有一个完整的控制，可以使用`DB门面`的`beginTransaction`方法：

```
DB::beginTransaction();
```

你可以通过`rollBack`方法回滚事务：

```
DB::rollBack();
```

最后，可以通过`commit`方法提交事务：

```
DB::commit();
```

_注意：使用`DB门面`的事务方法还可以用于控制查询构建器和 `Eloquent ORM` 的事务。_

--------------------------------------------------------------------------------

## 7.1.4 使用多个数据库连接

使用多个数据库连接的时候，可以使用`DB门面`的`connection`方法访问每个连接。传递给`connection`方法的连接名对应配置文件`config/database.php`中相应的连接：

```
$users = DB::connection('foo')->select(...);
```

你还可以通过连接实例上的`getPdo`方法底层原生的 PDO 实例：

```
$pdo = DB::connection()->getPdo();
```

--------------------------------------------------------------------------------
