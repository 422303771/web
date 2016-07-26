## 8.2 关联关系

### 8.2.1 简介

数据表经常要与其它表做关联，比如一篇博客文章可能有很多评论，或者一个订单会被关联到下单用户，Eloquent 使得组织和处理这些关联关系变得简单，并且支持多种不同类型的关联关系：

* 一对一
* 一对多
* 多对多
* 远层一对多
* 多态关联
* 多对多的多态关联

------

### 8.2.2 定义关联关系

Eloquent 关联关系以Eloquent模型类方法的形式被定义。和 Eloquent 模型本身一样，关联关系也是强大的查询构建器，定义关联关系为函数能够提供功能强大的方法链和查询能力。

例如：

	$user->posts()->where('active', 1)->get();

但是，在深入使用关联关系之前，让我们先学习如何定义每种关联类型。

#### 8.2.2.1 一对一

一对一关联是一个非常简单的关联关系，例如，一个`User`模型有一个与之对应的`Phone`模型。要定义这种模型，我们需要将`phone`方法置于`User`模型中，`phone`方法应该返回 Eloquent 模型基类,上的`hasOne`
方法：

在User模型中，使用`hasOne`方法，设置`photo`方法的访问。

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 获取关联到用户的手机
     */
    public function phone()
    {
        return $this->hasOne('App\Phone');
    }
}
```
传递给`hasOne`方法的第一个参数是关联模型的名称，关联关系被定义后，我们可以使用 Eloquent 的动态属性获取关联记录。

动态属性允许我们访问关联函数就像它们是定义在模型上的属性一样：

```php
$phone = User::find(1)->phone;
```
Eloquent 默认关联关系的外键基于模型名称，在本例中，`Phone`模型默认有一个`user_id`外键，如果你希望重写这种约定，可以传递第二个参数到`hasOne`方法：

```php
return $this->hasOne('App\Phone', 'foreign_key');
```

此外，Eloquent 假设外键应该在父级上有一个与之匹配的`id`，换句话说，Eloquent 将会通过`user`表的`id`值去`phone`表中查询`user_id`与之匹配的`Phone`记录。

如果你想要关联关系使用其他值而不是`id`，可以传递第三个参数到`hasOne`来指定自定义的主键：

```
return $this->hasOne('App\Phone', 'foreign_key', 'local_key');
```	

* **定义相对的关联**

	我们可以从`User`中访问`Phone`模型，相应的，我们也可以在`Phone`模型中定义关联关系从而让我们可以拥有该`phone`的`User`。我们可以使用`belongsTo`方法定义与`hasOne`关联关系相对的关联：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Phone extends Model{
	    /**
	     * 获取手机对应的用户
	     */
	    public function user()
	    {
	        return $this->belongsTo('App\User');
	    }
	}
	```
	在上面的例子中，Eloquent 将会尝试通过`Phone`模型的`user_id`去`User`模型查找与之匹配的记录。Eloquent 通过关联关系方法名并在方法名后加`_id`后缀来生成默认的外键名。然而，如果`Phone`模型上的外键不是`user_id`，也可以将自定义的键名作为第二个参数传递到`belongsTo`方法：
	
	```php
	/**
	 * 获取手机对应的用户
	 */
	public function user(){
	    return $this->belongsTo('App\User', 'foreign_key');
	}
	```
	如果父模型不使用id作为主键，或者你希望使用别的列来连接子模型，可以将父表自定义键作为第三个参数传递给belongsTo方法：
	
	```php
	/**
	 * 获取手机对应的用户
	 */
	public function user(){
	    return $this->belongsTo('App\User', 'foreign_key', 'other_key');
	}
	```

#### 8.2.2.2 一对多

“一对多”是用于定义单个模型拥有多个其它模型的关联关系。

例如，一篇博客文章拥有无数评论，和其他关联关系一样，一对多关联通过在 Eloquent 模型中定义方法来定义：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Post extends Model{
    /**
     * 获取博客文章的评论
     */
    public function comments()
    {
        return $this->hasMany('App\Comment');
    }
}
```
记住，Eloquent 会自动判断`Comment`模型的外键，为方便起见，Eloquent 将拥有者模型名称加上`id`后缀作为外键。因此，在本例中，Eloquent 假设`Comment`模型上的外键是`post_id`。

关联关系被定义后，我们就可以通过访问`comments`属性来访问评论集合。记住，由于 Eloquent 提供“动态属性”，我们可以像访问模型的属性一样访问关联方法：

```php
$comments = App\Post::find(1)->comments;

foreach ($comments as $comment) {
    //
}
```
当然，由于所有关联同时也是查询构建器，我们可以添加更多的条件约束到通过调用`comments`方法获取到的评论上：

```
$comments = App\Post::find(1)->comments()->where('title', 'foo')->first();
```
和`hasOne`方法一样，你还可以通过传递额外参数到`hasMany`方法来重新设置外键和本地主键：

```
return $this->hasMany('App\Comment', 'foreign_key');
return $this->hasMany('App\Comment', 'foreign_key', 'local_key');
```

* **定义相对的关联**

	现在我们可以访问文章的所有评论了，接下来让我们定义一个关联关系允许通过评论访问所属文章。要定义与`hasMany`相对的关联关系，需要在子模型中定义一个关联方法去调用`belongsTo`方法：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Comment extends Model{
	    /**
	     * 获取评论对应的博客文章
	     */
	    public function post()
	    {
	        return $this->belongsTo('App\Post');
	    }
	}
	```
	关联关系定义好之后，我们可以通过访问动态属性`post`来获取一条`Comment`对应的`Post`：
	
	```php
	$comment = App\Comment::find(1);
	echo $comment->post->title;
	```
	在上面这个例子中，Eloquent 尝试匹配`Comment`模型的`post_id`与`Post`模型的`id`，Eloquent 通过关联方法名加上`_id`后缀生成默认外键，当然，你也可以通过传递自定义外键名作为第二个参数传递到`belongsTo`方法，如果你的外键不是`post_id`，或者你想自定义的话：
	
	```php
	/**
	 * 获取评论对应的博客文章
	 */
	public function post(){
	    return $this->belongsTo('App\Post', 'foreign_key');
	}
	```
	如果你的父模型不使用`id`作为主键，或者你希望通过其他列来连接子模型，可以将自定义键名作为第三个参数传递给`belongsTo`方法：
	
	```php
	/**
	 * 获取评论对应的博客文章
	 */
	public function post(){
	    return $this->belongsTo('App\Post', 'foreign_key', 'other_key');
	}
	```

#### 8.2.2.3 多对多

多对多关系比`hasOne`和`hasMany`关联关系要稍微复杂一些。

这种关联关系的一个例子就是一个用户有多个角色，同时一个角色被多个用户共用。例如，很多用户可能都有一个`Admin`角色。要定义这样的关联关系，需要三个数据表：`users`、`roles`和`role_user`，`role_user`表按照关联模型名的字母顺序命名，并且包含`user_id`和`role_id`两个列。

多对多关联通过编写一个调用 Eloquent 基类上的`belongsToMany`方法的函数来定义：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 用户角色
     */
    public function roles()
    {
        return $this->belongsToMany('App\Role');
    }
}
```
关联关系被定义之后，可以使用动态属性`roles`来访问用户的角色：

```php
$user = App\User::find(1);
foreach ($user->roles as $role) {
    //
}
```
当然，和所有其它关联关系类型一样，你可以调用`roles`方法来添加条件约束到关联查询上：

```php
$roles = App\User::find(1)->roles()->orderBy('name')->get();
```
正如前面所提到的，为了决定关联关系连接表的表名，Eloquent以字母顺序连接两个关联模型的名字。然而，你可以重写这种约定——通过传递第二个参数到`belongsToMany`方法：

```php
return $this->belongsToMany('App\Role', 'user_roles');
```
除了自定义连接表的表名，你还可以通过传递额外参数到`belongsToMany`方法来自定义该表中字段的列名。

第三个参数是你定义的关系模型的外键名称，第四个参数你要连接到的模型的外键名称：

```php
return $this->belongsToMany('App\Role', 'user_roles', 'user_id', 'role_id');
```

* **定义相对的关联关系**

	要定义与多对多关联相对的关联关系，只需在关联模型中在调用一下`belongsToMany`方法即可。让我们在`Role`模型中定义`users`方法：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Role extends Model{
	    /**
	     * 角色用户
	     */
	    public function users()
	    {
	        return $this->belongsToMany('App\User');
	    }
	}
	```
	正如你所看到的，定义的关联关系和与其对应的`User`中定义的一模一样，只是前者引用`App\Role`，后者引用`App\User`，由于我们再次使用了`belongsToMany`方法，所有的常用表和键自定义选项在定义与多对多相对的关联关系时都是可用的。

* **获取中间表的列**

	正如你已经学习到的，处理多对多关联要求一个中间表。Eloquent 提供了一些有用的方法来与其进行交互。
	
	例如，我们假设`User`对象有很多与之关联的`Role`对象，访问这些关联关系之后，我们可以使用模型上的`pivot`属性访问中间表：
	
	```php
	$user = App\User::find(1);
	
	foreach ($user->roles as $role) {
	    echo $role->pivot->created_at;
	}
	```
	注意我们获取到的每一个`Role`模型都被自动赋上了`pivot`属性。该属性包含一个代表中间表的模型，并且可以像其它 Eloquent 模型一样使用。
	
	默认情况下，只有模型键才能用在pivot对象上，如果你的pivot表包含额外的属性，必须在定义关联关系时进行指定：
	
	```php
	return $this->belongsToMany('App\Role')->withPivot('column1', 'column2');
	```
	如果你想要你的`pivot`表自动包含`created_at`和`updated_at`时间戳，在关联关系定义时使用`withTimestamps`方法：
	
	```php
	return $this->belongsToMany('App\Role')->withTimestamps();
	```

* **定义中间表过滤**

	你还可以对`belongsToMany`的返回结果使用`wherePivot`与`wherePivotIn`过滤：
	
	```php
	return $this->belongsToMany('App\Role')->wherePivot('approved', 1);
	
	return $this->belongsToMany('App\Role')->wherePivotIn('approved', [1, 2]);
	```

#### 8.2.2.4 远层的一对多

“远层一对多”关联为通过中间关联访问远层的关联关系提供了一个便利之道。

例如：`Country`模型通过中间的`User`模型可能拥有多个`Post`模型。在这个例子中，你可以轻易的找到给定国家的所有文章，让我们看看定义这个关联关系需要哪些表：

```
countries
    id - integer
    name - string

users
    id - integer
    country_id - integer
    name - string

posts
    id - integer
    user_id - integer
    title - string
```
尽管`posts`表不包含`country_id`列，`hasManyThrough`关联提供了通过`$country->posts`来访问一个国家的所有文章。

要执行该查询，Eloquent 在中间表`$users`上检查`country_id`，查找到相匹配的用户`ID`后，通过用户`ID`来查询`posts`表。

既然我们已经查看了该关联关系的数据表结构，接下来让我们在`Country`模型上进行定义：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Country extends Model{
    /**
     * 获取指定国家的所有文章
     */
    public function posts()
    {
        return $this->hasManyThrough('App\Post', 'App\User');
    }
}
```

第一个传递到`hasManyThrough`方法的参数是最终我们希望访问的模型的名称，第二个参数是中间模型名称。

当执行这种关联查询时通常 Eloquent 外键规则会被使用，如果你想要自定义该关联关系的外键，可以将它们作为第三个、第四个参数传递给`hasManyThrough`方法。第三个参数是中间模型的外键名，第四个参数是最终模型的外键名。

```php
class Country extends Model{
    public function posts()
    {
        return $this->hasManyThrough('App\Post', 'App\User', 'country_id', 'user_id');
    }
}
```

#### 8.2.2.5 多态关联

* **表结构**

多态关联允许一个模型在单个关联下属于多个不同模型。

例如，假如应用用户可以对文章点赞也可以对评论点赞，使用多态关联，你可以在这两种场景下使用单个`likes`表，首先，让我们看看构建这种关联关系需要的表结构：

```
posts
    id - integer
    title - string
    body - text

comments
    id - integer
    post_id - integer
    body - text

likes
    id - integer
    likeable_id - integer
    likeable_type - string
```
两个重要的需要注意的列是 `likes` 表上的 `likeable_id` 和 `likeable_type`。

`likeable_id`列对应 `Post` 或 `Comment` 的 `ID` 值，而 `likeable_type` 列对应所属模型的类名。当访问 `likeable` 关联时，ORM 根据 `likeable_type` 列来判断所属模型的类型并返回相应模型实例。

* **模型结构**

	接下来，让我们看看构建这种关联关系需要在模型中定义什么：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Like extends Model{
	    /**
	     * 获取所属的likeable模型
	     */
	    public function likeable()
	    {
	        return $this->morphTo();
	    }
	}
	
	class Post extends Model{
	    /**
	     * 获取该文章的所有点赞
	     */
	    public function likes()
	    {
	        return $this->morphMany('App\Like', 'likeable');
	    }
	}
	
	class Comment extends Model{
	    /**
	     * 获取该评论的所有点赞
	     */
	    public function likes()
	    {
	        return $this->morphMany('App\Like', 'likeable');
	    }
	}
	```

* **获取多态关联**

	数据表和模型定义好以后，可以通过模型访问关联关系。
	
	例如，要访问一篇文章的所有点赞，可以通过使用动态属性likes ：
	
	```php
	$post = App\Post::find(1);
	
	foreach ($post->likes as $like) {
	    //
	}
	```
	你还可以通过调用 `morphTo` 方法从多态模型中获取多态关联的所属对象。在本例中，就是 `Like` 模型中的 `likeable` 方法。因此，我们可以用动态属性的方式访问该方法：
	
	```
	$like = App\Like::find(1);
	$likeable = $like->likeable;
	```
	`Like` 模型的 `likeable` 关联返回 `Post` 或 `Comment` 实例，这取决于哪个类型的模型拥有该点赞。

#### 8.2.2.6 多对多的多态关联

* **表结构**

	除了传统的多态关联，还可以定义“多对多”的多态关联，例如，一个博客的 `Post` 和 `Video` 模型可能共享一个 `Tag` 模型的多态关联。
	
	使用多对多的多态关联允许你在博客文章和视频之间有唯一的标签列表。
	
	首先，让我们看看表结构：
	
	```
	posts
	    id - integer
	    name - string
	
	videos
	    id - integer
	    name - string
	
	tags
	    id - integer
	    name - string
	
	taggables
	    tag_id - integer
	    taggable_id - integer
	    taggable_type - string
	```

* **模型结构**

	接下来，我们准备在模型中定义该关联关系。`Post` 和 `Video` 模型都有一个 `tags` 方法调用 Eloquent 基类的 `morphToMany` 方法：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Post extends Model{
	    /**
	     * 获取指定文章所有标签
	     */
	    public function tags()
	    {
	        return $this->morphToMany('App\Tag', 'taggable');
	    }
	}
	```

* **定义相对的关联关系**

	接下来，在`Tag`模型中，应该为每一个关联模型定义一个方法。
	
	例如，我们定义一个`posts`方法和`videos`方法：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Database\Eloquent\Model;
	
	class Tag extends Model{
	    /**
	     * 获取所有分配该标签的文章
	     */
	    public function posts()
	    {
	        return $this->morphedByMany('App\Post', 'taggable');
	    }
	
	    /**
	     * 获取分配该标签的所有视频
	     */
	    public function videos()
	    {
	        return $this->morphedByMany('App\Video', 'taggable');
	    }
	}
	```

* **获取关联关系**
	
	定义好数据库和模型后可以通过模型访问关联关系。
	
	例如，要访问一篇文章的所有标签，可以使用动态属性tags：
	
	```php
	$post = App\Post::find(1);
	
	foreach ($post->tags as $tag) {
	    //
	}
	```
	还可以通过访问调用`morphedByMany`的方法名从多态模型中获取多态关联的所属对象。
	
	在本例中，就是`Tag`模型中的`posts`或者`videos`方法：
	
	```php
	$tag = App\Tag::find(1);
	
	foreach ($tag->videos as $video) {
	    //
	}
	```

----

### 8.2.3 关联查询

由于 Eloquent 所有关联关系都是通过函数定义，你可以调用这些方法来获取关联关系的实例而不需要再去手动执行关联查询。此外，所有 Eloquent 关联关系类型同时也是查询构建器，允许你在最终在数据库执行 SQL 之前继续添加条件约束到关联查询上。

例如，假定在一个博客系统中一个 User 模型有很多相关的 Post 模型：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class User extends Model{
    /**
     * 获取指定用户的所有文章
     */
    public function posts()
    {
        return $this->hasMany('App\Post');
    }
}
```
你可以像这样查询`posts`关联并添加额外的条件约束到该关联关系上：

```
$user = App\User::find(1);
$user->posts()->where('active', 1)->get();
```

#### 8.2.3.1 关联关系方法 VS 动态属性

如果你不需要添加额外的条件约束到 Eloquent 关联查询，你可以简单通过动态属性来访问关联对象。

例如，还是拿`User`和`Post`模型作为例子，你可以像这样访问所有用户的文章：

```php
$user = App\User::find(1);

foreach ($user->posts as $post) {
    //
}
```
动态属性就是“懒惰式加载”，意味着当你真正访问它们的时候才会加载关联数据。

正因为如此，开发者经常使用渴求式加载来预加载他们知道在加载模型时要被访问的关联关系。

渴求式加载有效减少了必须要被执以加载模型关联的 SQL 查询。

#### 8.2.3.2 查询已存在的关联关系

访问一个模型的记录的时候，你可能希望基于关联关系是否存在来限制查询结果的数目。

例如，假设你想要获取所有至少有一个评论的博客文章，要实现这个，可以传递关联关系的名称到`has`方法：

```php
// 获取所有至少有一条评论的文章...
$posts = App\Post::has('comments')->get();
```
你还可以指定操作符和大小来自定义查询：

```php
// 获取所有至少有三条评论的文章...
$posts = Post::has('comments', '>=', 3)->get();
```
还可以使用`.`来构造嵌套`has`语句。

例如，你要获取所有至少有一条评论及投票的所有文章：

```php
// 获取所有至少有一条评论获得投票的文章...
$posts = Post::has('comments.votes')->get();
```
如果你需要更强大的功能，可以使用`whereHas`和`orWhereHas`方法将`where`条件放到`has`查询上，这些方法允许你添加自定义条件约束到关联关系条件约束，例如检查一条评论的内容：

```php
// 获取所有至少有一条评论包含foo字样的文章
$posts = Post::whereHas('comments', function ($query) {
    $query->where('content', 'like', 'foo%');
})->get();
```

#### 8.2.3.3 渴求式加载

当以属性方式访问数据库关联关系的时候，关联关系数据是`懒惰式加载`的，这意味着关联关系数据直到第一次访问的时候才被加载。

然而，Eloquent 可以在查询父级模型的同时，`渴求式加载`关联关系。渴求式加载缓解了`N+1`查询问题，要阐明`N+1`查询问题，考虑下关联到`Author`的`Book`模型：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Book extends Model{
    /**
     * 获取写这本书的作者
     */
    public function author()
    {
        return $this->belongsTo('App\Author');
    }
}
```
现在，让我们获取所有书及其作者：

```php
$books = App\Book::all();

foreach ($books as $book) {
    echo $book->author->name;
}
```

该循环先执行1次查询获取表中的所有书，然后另一个查询获取每一本书的作者，因此，如果有25本书，要执行26次查询：1次是获取书本身，剩下的25次查询是为每一本书获取其作者。

谢天谢地，我们可以使用渴求式加载来减少该操作到2次查询。当查询的时候，可以使用`with`方法指定应该被渴求式加载的关联关系：

```php
$books = App\Book::with('author')->get();

foreach ($books as $book) {
    echo $book->author->name;
}
```
在该操作中，只执行两次查询即可：

```
select * from books
select * from authors where id in (1, 2, 3, 4, 5, ...)
```


* **渴求式加载多个关联关系**

	有时候你需要在单个操作中渴求式加载几个不同的关联关系。要实现这个，只需要添加额外的参数到`with`方法即可：
	
		$books = App\Book::with('author', 'publisher')->get();

* **嵌套的渴求式加载**
	
	要渴求式加载嵌套的关联关系，可以使用`.`语法。例如，让我们在一个Eloquent语句中渴求式加载所有书的作者及所有作者的个人联系方式：
	
		$books = App\Book::with('author.contacts')->get();

#### 8.2.3.4 带条件约束的渴求式加载

有时候我们希望渴求式加载一个关联关系，但还想为渴求式加载指定更多的查询条件：

```php
$users = App\User::with(['posts' => function ($query) {
    $query->where('title', 'like', '%first%');
}])->get();
```
在这个例子中，Eloquent 只渴求式加载 title 包含 first 的文章。当然，你可以调用其它查询构建器来自定义渴求式加载操作：

```php
$users = App\User::with(['posts' => function ($query) {
    $query->orderBy('created_at', 'desc');
}])->get();
```

#### 8.2.3.5 懒惰渴求式加载

有时候你需要在父模型已经被获取后,渴求式加载一个关联关系。

例如，这在你需要动态决定是否加载关联模型时可能很有用：

```php
$books = App\Book::all();

if ($someCondition) {
    $books->load('author', 'publisher');
}
```
如果你需要设置更多的查询条件到渴求式加载查询上，可以传递一个闭包到`load`方法：

```php
$books->load(['author' => function ($query) {
    $query->orderBy('published_date', 'asc');
}]);
```

-----

### 8.2.4 插入关联模型

#### 8.2.4.1 基本使用

* **save方法**
	
	Eloquent 提供了便利的方法来添加新模型到关联关系。
	
	例如，也许你需要插入新的`Comment`到`Post`模型，你可以从关联关系的`save`方法直接插入`Comment`而不是手动设置`Comment`的`post_id`属性：
	```php
	$comment = new App\Comment(['message' => 'A new comment.']);
	$post = App\Post::find(1);
	$post->comments()->save($comment);
	```
	注意我们没有用动态属性方式访问`comments`，而是调用`comments`方法获取关联关系实例。`save`方法会自动添加`post_id`值到新的`Comment`模型。
	
	如果你需要保存多个关联模型，可以使用`saveMany`方法：
	
	```
	$post = App\Post::find(1);
	
	$post->comments()->saveMany([
	    new App\Comment(['message' => 'A new comment.']),
	    new App\Comment(['message' => 'Another comment.']),
	]);
	```

* **save & 多对多关联**
	
	当处理多对多关联的时候，`save`方法以数组形式接收额外的中间表属性作为第二个参数：
	
		App\User::find(1)->roles()->save($role, ['expires' => $expires]);

* **create方法**
	
	除了`save`和`saveMany`方法外，还可以使用`create`方法，该方法接收属性数组、创建模型、然后插入数据库。`save`和`create`的不同之处在于`save`接收整个Eloquent模型实例而`create`接收原生PHP数组：
	
	```php
	$post = App\Post::find(1);
	
	$comment = $post->comments()->create([
	    'message' => 'A new comment.',
	]);
	```
	使用`create`方法之前确保先浏览属性[批量赋值]文档(http://laravelacademy.org/post/2995.html)。

#### 8.2.4.2 更新”属于“关联

更新`belongsTo`关联的时候，可以使用`associate`方法，该方法会在子模型设置外键：

```php
$account = App\Account::find(10);
$user->account()->associate($account);
$user->save();
```
移除`belongsTo`关联的时候，可以使用`dissociate`方法。该方法在子模型上取消外键和关联：

```php
$user->account()->dissociate();
$user->save();
```

#### 8.2.4.3 多对多关联

* **附加/分离**
	
	处理多对多关联的时候，Eloquent提供了一些额外的帮助函数来使得处理关联模型变得更加方便。
	
	例如，让我们假定一个用户可能有多个角色同时一个角色属于多个用户，要通过在连接模型的中间表中插入记录附加角色到用户上，可以使用`attach`方法：
	
	```php
	$user = App\User::find(1);
	$user->roles()->attach($roleId);
	```
	附加关联关系到模型，还可以以数组形式传递额外被插入数据到中间表：
	
	```
	$user->roles()->attach($roleId, ['expires' => $expires]);
	```
	当然，有时候有必要从用户中移除角色，要移除一个多对多关联记录，使用`detach`方法。`detach`方法将会从中间表中移除相应的记录；然而，两个模型在数据库中都保持不变：
	
	```
	// 从指定用户中移除角色...
	$user->roles()->detach($roleId);
	// 从指定用户移除所有角色...
	$user->roles()->detach();
	```
	为了方便，`attach`和`detach`还接收数组形式的 ID 作为输入：
	
	```
	$user = App\User::find(1);
	$user->roles()->detach([1, 2, 3]);
	$user->roles()->attach([1 => ['expires' => $expires], 2, 3]);
	```

* **同步**

	你还可以使用`sync`方法构建多对多关联。`sync`方法接收数组形式的`ID`并将其放置到中间表。任何不在该数组中的`ID`对应记录将会从中间表中移除。因此，该操作完成后，只有在数组中的`ID`对应记录还存在于中间表：
	
	```
	$user->roles()->sync([1, 2, 3]);
	```
	你还可以和ID一起传递额外的中间表值:
	
	```
	$user->roles()->sync([1 => ['expires' => true], 2, 3]);
	```

#### 8.2.4.4 触发父级时间戳

当一个模型属于另外一个时，例如`Comment`属于`Post`，子模型更新时父模型的时间戳也被更新将很有用。

例如，当`Comment`模型被更新时，你可能想要`触发`创建其所属模型`Post`的`updated_at`时间戳。

Eloquent使得这项操作变得简单，只需要添加包含关联关系名称的`touches`属性到子模型中即可：

```php
<?php

namespace App;

use Illuminate\Database\Eloquent\Model;

class Comment extends Model{
    /**
     * 要触发的所有关联关系
     *
     * @var array
     */
    protected $touches = ['post'];

    /**
     * 评论所属文章
     */
    public function post()
    {
        return $this->belongsTo('App\Post');
    }
}
```
现在，当你更新`Comment`时，所属模型`Post`将也会更新其`updated_at`值：

```php
$comment = App\Comment::find(1);
$comment->text = 'Edit to this comment!';
$comment->save();
```
