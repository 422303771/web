<!--toc-->

- [8.4 访问器&修改器](#84-访问器修改器)
	- [8.4.1 简介](#841-简介)
	- [8.4.2 访问器 & 修改器](#842-访问器-修改器)
		- [8.4.2.1 定义访问器](#8421-定义访问器)
		- [8.4.2.2 定义修改器](#8422-定义修改器)
	- [8.4.3 日期修改器](#843-日期修改器)
	- [8.4.4 属性转换](#844-属性转换)
		- [8.4.4.1 数组转换](#8441-数组转换)

<!-- tocstop -->

----

# 8.4 访问器&修改器

## 8.4.1 简介

`访问器`和`修改器`允许你在获取模型属性或设置其值时格式化 Eloquent 属性。

例如，你可能想要使用Laravel加密器对存储在数据库中的数据进行加密，并且在 Eloquent 模型中访问时自动进行解密。

除了自定义访问器和修改器，Eloquent 还可以自动转换日期字段为Carbon实例，甚至将文本转换为JSON。

--------------------------------------------------------------------------------

## 8.4.2 访问器 & 修改器

### 8.4.2.1 定义访问器

要定义一个访问器，需要在模型中创建一个`getFooAttribute`方法，其中`Foo`是你想要访问的字段名（使用驼峰式命名规则）。

在本例中，我们将会为`first_name`属性定义一个访问器，该访问器在获取`first_name`的值时被 Eloquent 自动调用：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 获取用户的名字
     *
     * @param  string  $value
     * @return string
     */
    public function getFirstNameAttribute($value)
    {
        return ucfirst($value);
    }
}
```

正如你所看到的，该字段的原生值被传递给访问器，然后返回处理过的值。

要访问该值只需要简单访问`first_name`即可：

```php
$user = App\User::find(1);
$firstName = $user->first_name;
```

### 8.4.2.2 定义修改器

要定义一个修改器，需要在模型中定义`setFooAttribute`方法，其中`Foo`是你想要访问的字段（使用驼峰式命名规则）。

接下来让我们为`first_name`属性定义一个修改器，当我们为模型上的`first_name`赋值时该修改器会被自动调用：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 设置用户的名字
     *
     * @param  string  $value
     * @return string
     */
    public function setFirstNameAttribute($value)
    {
        $this->attributes['first_name'] = strtolower($value);
    }
}
```

该修改器获取要被设置的属性值，允许你操纵该值并设置 Eloquent 模型内部属性值为操作后的值。

例如，如果你尝试设置`Sally`的`first_name`属性：

```php
$user = App\User::find(1);
$user->first_name = 'Sally';
```

在本例中，`setFirstNameAttribute`方法会被调用，传入参数为`Sally`，修改器会对其调用`strtolower`函数并将处理后的值设置为内部属性的值。

--------------------------------------------------------------------------------

## 8.4.3 日期修改器

默认情况下，Eloquent 将会转化`created_at`和`updated_at`列的值为 `Carbon` 实例，该类继承自 PHP 原生的`Datetime`类，并提供了各种有用的方法。

你可以自定义哪些字段被自动调整修改，甚至可以通过重写模型中的`$dates`属性完全禁止调整：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 应该被调整为日期的属性
     *
     * @var array
     */
    protected $dates = ['created_at', 'updated_at', 'disabled_at'];
}
```

如果字段是日期格式时，你可以将其值设置为 UNIX 时间戳，日期字符串（`Y-m-d`），日期-时间字符串，`Datetime/Carbon`实例，日期的值将会自动以正确格式存储到数据库中：

```php
$user = App\User::find(1);
$user->disabled_at = Carbon::now();
$user->save();
```

正如上面提到的，当获取被罗列在`$dates`数组中的属性时，它们会被自动转化为`Carbon`实例，允许你在属性上使用任何`Carbon`的方法：

```php
$user = App\User::find(1);
return $user->disabled_at->getTimestamp();
```

默认情况下，时间戳的格式是`Y-m-d H:i:s`，如果你需要自定义时间戳格式，在模型中设置`$dateFormat`属性，该属性决定日期属性存储在数据库以及序列化为数组或 `JSON` 时的格式：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Flight extends Model{
    /**
     * 模型日期的存储格式
     *
     * @var string
     */
    protected $dateFormat = 'U';
}
```

--------------------------------------------------------------------------------

## 8.4.4 属性转换

模型中的`$casts`属性为属性字段转换到通用数据类型提供了便利方法 。`$casts`属性是数组格式，其键是要被转换的属性名称，其值时你想要转换的类型。目前支持的转换类型包括：`integer`, `real`, `float`, `double`, `string`, `boolean`, `object`，`array`，`collection`，`date`和`datetime`。

例如，让我们转换`is_admin`属性，将其由`integer`类型（0或1）转换为`boolean`类型：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 应该被转化为原生类型的属性
     *
     * @var array
     */
    protected $casts = [
        'is_admin' => 'boolean',
    ];
}
```

现在，`is_admin`属性在被访问时总是被转换为`boolean`，即使底层存储在数据库中的值是`integer`：

```php
$user = App\User::find(1);

if ($user->is_admin) {
    //
}
```

### 8.4.4.1 数组转换

`array`类型转换在处理被存储为,序列化 JSON 格式的字段时特别有用。

例如，如果数据库有一个`TEXT`字段类型包含了序列化`JSON`，添加`array`类型转换到该属性将会在 Eloquent 模型中访问其值时自动将其反序列化为`PHP`数组：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 应该被转化为原生类型的属性
     *
     * @var array
     */
    protected $casts = [
        'options' => 'array',
    ];
}
```

类型转换被定义后，访问`options`属性将会自动从 `JSON` 反序列化为 `PHP` 数组，反之，当你设置`options`属性的值时，给定数组将会自动转化为 `JSON` 以供存储：

```php
$user = App\User::find(1);
$options = $user->options;
$options['key'] = 'value';
$user->options = $options;
$user->save();
```
