<!--toc-->

- [8.3 集合](#83-集合)
	- [8.3.1 简介](#831-简介)
	- [8.3.2 可用方法](#832-可用方法)
	- [8.3.3 自定义集合](#833-自定义集合)

<!-- tocstop -->

----

# 8.3 集合

## 8.3.1 简介

Eloquent 返回的所有的,包含多条记录的,结果集,都是 `Illuminate\Database\Eloquent\Collection`对象的实例，包括通过 `get` 方法或者,通过访问关联关系获取的结果。Eloquent 集合对象继承自`Laravel`的集合基类，因此很自然的继承了很多处理 Eloquent 模型底层数组的方法。

当然，所有集合也是迭代器，允许你像数组一样对其进行循环：

```php
$users = App\User::where('active', 1)->get();

foreach ($users as $user) {
    echo $user->name;
}
```

然而，集合使用直观接口提供各种映射/简化操作，因此比数组更加强大。

例如，我们可以通过以下方式移除所有无效的模型并聚合还存在的用户的名字

```php
$users = App\User::where('active', 1)->get();

$names = $users->reject(function ($user) {
    return $user->active === false;
})->map(function ($user) {
    return $user->name;
});
```

_注意：尽管大多数 Eloquent 集合返回的是一个新的 Eloquent 集合实例，但是`pluck`、`keys`、`zip`、`collapse`、`flatten`和`flip`方法返回的是集合基类实例。_

--------------------------------------------------------------------------------

## 8.3.2 可用方法

关于服务的可用方法，可用查看[服务-合集](http://laravelacademy.org/post/178.html#ipt_kb_toc_178_3)

## 8.3.3 自定义集合

如果你需要在自己扩展的方法中使用自定义的集合对象，可以重写模型上的 `newCollection` 方法：

```php
<?php

namespace App;

use App\CustomCollection;
use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 创建一个新的Eloquent集合实例
     *
     * @param  array  $models
     * @return \Illuminate\Database\Eloquent\Collection
     */
    public function newCollection(array $models = [])
    {
        return new CustomCollection($models);
    }
}
```

定义好 `newCollection` 方法后，无论何时 Eloquent 返回该模型的 `Collection` 实例你都会获取到自定义的集合。

如果你想要在应用中的每一个模型中使用自定义集合，需要在模型基类中重写 `newCollection` 方法。
