## 7.4 填充数据

### 7.4.1 简介

Laravel 包含了一个简单方法来填充数据库——使用填充类和测试数据。

所有的填充类都位于`database/seeds`目录。

填充类的类名完全由你自定义，但最好还是遵循一定的规则，比如可读性,例如`UserTableSeeder`等等。

安装完 Laravel 后，会默认提供一个`DatabaseSeeder`类。

从这个类中，你可以使用`call`方法来运行其他填充类，从而允许你控制填充顺序。

-----

### 7.4.2 编写填充器

要生成一个填充器，可以通过 Artisan 命令`make:seeder`。所有框架生成的填充器都位于`database/seeders`目录：

	php artisan make:seeder UserTableSeeder

一个填充器类默认只包含一个方法：`run`。当Artisan命令`db:seed`运行时该方法被调用。在`run`方法中，可以插入任何你想插入数据库的数据，你可以使用查询构建器手动插入数据，也可以使用 Eloquent 模型工厂。

举个例子，让我们修改 Laravel 安装时自带的`DatabaseSeeder`类，添加一个数据库插入语句到`run`方法：

```
<?php

use Illuminate\Database\Seeder;
use Illuminate\Database\Eloquent\Model;

class DatabaseSeeder extends Seeder{
    /**
     * 运行数据库填充
     *
     * @return void
     */
    public function run()
    {
        DB::table('users')->insert([
            'name' => str_random(10),
            'email' => str_random(10).'@gmail.com',
            'password' => bcrypt('secret'),
        ]);
    }
}
```

#### 7.4.2.1 使用模型工厂

当然，手动指定每一个模型填充的属性是很笨重累赘的，取而代之的，我们可以使用模型工厂来方便的生成大量的数据库记录。首先，查看模型工厂文档来学习如何定义工厂，定义工厂后，可以使用帮助函数`factory`来插入记录到数据库。

举个例子，让我们创建50个用户并添加关联关系到每个用户：

```
/**
 * 运行数据库填充
 *
 * @return void
 */
public function run(){
    factory('App\User', 50)->create()->each(function($u) {
        $u->posts()->save(factory('App\Post')->make());
    });
}
```

#### 7.4.2.2 调用额外的填充器

在`DatabaseSeeder`类中，你可以使用`call`方法执行额外的填充类，使用`call`方法允许你将数据库填充分解成多个文件，这样单个填充器类就不会变得无比巨大，只需简单将你想要运行的填充器类名传递过去即可：

```
/**
* 运行数据库填充
*
* @return void
*/
public function run(){
    Model::unguard();

    $this->call(UserTableSeeder::class);
    $this->call(PostsTableSeeder::class);
    $this->call(CommentsTableSeeder::class);

    Model::reguard();
}
```
----

### 7.4.3 运行填充器

编写好填充器类之后，可以使用 Artisan 命令`db:seed`来填充数据库。默认情况下，`db:seed`命令运行可以用来运行其它填充器类的`DatabaseSeeder`类，但是，你也可以使用`--class `选项来指定你想要运行的独立的填充器类：

```
php artisan db:seed
php artisan db:seed --class=UserTableSeeder
```
你还可以使用`migrate:refresh`命令来填充数据库，该命令还可以回滚并重新运行迁移，这在需要完全重建数据库时很有用：

	php artisan migrate:refresh --seed

-----
