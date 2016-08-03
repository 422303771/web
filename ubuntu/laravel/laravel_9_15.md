# 9.15 邮件

## 9.15.1 简介

`Laravel`基于`SwiftMailer`库提供了一套干净清爽的邮件API。Laravel为`SMTP`、`Mailgun`、`Mandrill`、`Amazon SES`、`PHP` 的`mail`函数，以及`sendmail`提供了驱动，从而允许你快速通过本地或云服务发送邮件。

### 9.15.1.1 邮件驱动预备知识

基于驱动的 API 如 `Mailgun`和 `Mandrill` 通常比`SMTP`服务器更简单、更快。所有的 API 驱动要求应用已经安装`Guzzle HTTP`库。你可以通过添加如下行到`composer.json`文件来安装`Guzzle`到项目：

```
"guzzlehttp/guzzle": "~5.3|~6.0"
```

- **Mailgun驱动**

  要使用`Mailgun`驱动（`Mailgun`前10000封邮件免费，后续收费），首先安装`Guzzle`，然后在配置文件`config/mail.php`中设置`driver`选项为`mailgun`。接下来，验证配置文件`config/services.php`包含如下选项：

  ```php
    'mailgun' => [
        'domain' => 'your-mailgun-domain',
        'secret' => 'your-mailgun-key',],
  ```

- **Mandrill驱动**

  要使用`Mandrill`驱动（Mandrill不支持中国区用户注册，汗！），首先安装`Guzzle`，然后在配置文件`config/mail.php`中设置`driver`选项值为`mandrill`。接下来，验证配置文件`config/services.php`包含如下选项：

  ```php
    'mandrill' => [
        'secret' => 'your-mandrill-key',],
  ```

- **SES驱动**

  要使用`Amazon SES`驱动（收费），安装`Amazon AWS`的`PHP SDK`，你可以通过添加如下行到`composer.json`文件的`require`部分来安装该库：

  ```
    "aws/aws-sdk-php": "~3.0"
  ```

  接下来，设置配置文件`config/mail.php`中的`driver`选项为`ses`。然后，验证配置文件`config/services.php`包含如下选项：

  ```php
    'ses' => [
        'key' => 'your-ses-key',
        'secret' => 'your-ses-secret',
        'region' => 'ses-region',  // e.g. us-east-1
    ],
  ```

--------------------------------------------------------------------------------

## 9.15.2 发送邮件

Laravel 允许你在视图中存储邮件信息。

例如，要组织你的电子邮件，可以在`resources/views`目录下创建`emails`目录。

要发送一条信息，使用`Mail`门面上的`send`方法。`send`方法接收三个参数。

第一个参数是包含邮件信息的视图名称； 第二个参数是你想要传递到该视图的数组数据； 第三个参数是接收消息实例的闭包回调----允许你自定义收件人、主题以及邮件其他方面的信息：

```php
<?php

namespace App\Http\Controllers;

use Mail;
use App\User;
use Illuminate\Http\Request;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 发送邮件给用户
     *
     * @param  Request  $request
     * @param  int  $id
     * @return Response
     */
    public function sendEmailReminder(Request $request, $id)
    {
        $user = User::findOrFail($id);

        Mail::send('emails.reminder', ['user' => $user], function ($m) use ($user) {
            $m->from('hello@app.com', 'Your Application');$m->to($user->email, $user->name)->subject('Your Reminder!');
        });
    }
}
```

由于我们在上例中传递一个包含`user`键的数组，我们可以在邮件中使用如下方式显示用户名：

```php
<?php echo $user->name; ?>
```

_注意：`$message`变量总是被传递到邮件视图，并允许嵌入附件，因此，你应该在视图负载中避免传入消息变量。_

- **构造消息**

  正如前面所讨论的，传递给`send`方法的第三个参数是一个允许你指定邮件消息本身多个选项的闭包。使用这个闭包可以指定消息的其他属性，例如抄送、群发，等等：

  ```php
    Mail::send('emails.welcome', $data, function ($message) {
        $message->from('us@example.com', 'Laravel');
        $message->to('foo@example.com')->cc('bar@example.com');
    });
  ```

  下面是`$message`消息构建器实例上的可用方法：

  ```php
    $message->from($address, $name = null);
    $message->sender($address, $name = null);
    $message->to($address, $name = null);
    $message->cc($address, $name = null);
    $message->bcc($address, $name = null);
    $message->replyTo($address, $name = null);
    $message->subject($subject);
    $message->priority($level);
    $message->attach($pathToFile, array $options = []);
    // 从$data字符串追加文件...
    $message->attachData($data, $name, array $options = []);
    // 获取底层SwiftMailer消息实例...
    $message->getSwiftMessage();
  ```

  _注意：传递给`Mail::send`闭包的消息实例继承自`SwiftMailer`消息类，该实例允许你调用该类上的任何方法来构建自己的电子邮件消息。_

- **纯文本邮件**

  默认情况下，传递给`send`方法的视图假定包含`HTML`，然而，通过传递数组作为第一个参数到`send`方法，你可以指定发送除`HTML`视图之外的纯文本视图：

  ```php
    Mail::send(['html.view', 'text.view'], $data, $callback);
  ```

  或者，如果你只需要发送纯文本邮件，可以指定在数组中使用`text`键：

  ```php
    Mail::send(['text' => 'view'], $data, $callback);
  ```

- **原生字符串邮件**

  如果你想要直接发送原生字符串邮件你可以使用`raw`方法：

  ```php
    Mail::raw('Text to e-mail', function ($message) {
        //
    });
  ```

### 9.15.2.1 附件

要添加附件到邮件，使用传递给闭包的`$message`对象上的`attach`方法。该方法接收文件的绝对路径作为第一个参数：

```php
Mail::send('emails.welcome', $data, function ($message) {
    //
    $message->attach($pathToFile);
});
```

当添加文件到消息时，你还可以通过传递数组作为第二个参数到attach方法来指定文件显示名和MIME类型：

```
$message->attach($pathToFile, ['as' => $display, 'mime' => $mime]);
```

### 9.15.2.2 内联附件

- **在邮件视图中嵌入一张图片**

  嵌套内联图片到邮件中通常是很笨重的，然而，`Laravel`提供了一个便捷的方式附加图片到邮件并获取相应的`CID`，要嵌入内联图片，在邮件视图中使用`$message`变量上的`embed`方法。记住，`Laravel`自动在所有邮件视图中传入`$message`变量使其有效：

  ```php
    <body>
        Here is an image:
        <img src="<?php echo $message->embed($pathToFile); ?>">
    </body>
  ```

- **在邮件视图中嵌入原生数据**

  如果你想要在邮件消息中嵌入原生数据字符串，可以使用`$message`变量上的`embedData`方法：

  ```php
    <body>
        Here is an image from raw data:
        <img src="<?php echo $message->embedData($data, $name); ?>">
    </body>
  ```

### 9.15.2.3 邮件队列

- **邮件消息队列**

  发送邮件消息可能会大幅度延长应用的响应时间，许多开发者选择将邮件发送放到队列中再后台执行，Laravel中可以使用内置的统一队列`API`来实现。要将邮件消息放到队列中，使用`Mail`门面上的`queue`方法：

  ```php
    Mail::queue('emails.welcome', $data, function ($message) {
        //
    });
  ```

  该方法自动将邮件任务推送到队列中以便在后台发送。当然，你需要在使用该特性前配置队列。

- **延迟消息队列**

  如果你想要延迟已经放到队列中邮件的发送，可以使用`later`方法。只需要传递你想要延迟发送的秒数作为第一个参数到该方法即可：

  ```php
    Mail::later(5, 'emails.welcome', $data, function ($message) {
        //
    });
  ```

- **推入指定队列**

  如果你想要将邮件消息推送到指定队列，可以使用`queueOn`和`laterOn`方法：

  ```php
    Mail::queueOn('queue-name', 'emails.welcome', $data, function ($message) {
        //
    });

    Mail::laterOn('queue-name', 5, 'emails.welcome', $data, function ($message) {
        //
    });
  ```

--------------------------------------------------------------------------------

## 9.15.3 邮件&本地开发

开发发送邮件的应用时，你可能不想要真的发送邮件到有效的电子邮件地址，而只是想要做下测试。`Laravel`提供了几种方式`禁止`邮件的实际发送。

- **日志驱动**

  一种解决方案是在本地开发时使用`log`邮件驱动。该驱动将所有邮件信息写到日志文件中以备查看，想要了解更多关于每个环境的应用配置信息，查看配置文档。

- **通用配置**

  `Laravel`提供的另一种解决方案是,为框架发送的所有邮件设置通用收件人，这样的话，所有应用生成的邮件将会被发送到指定地址，而不是实际发送邮件指定的地址。这可以通过在配置文件`config/mail.php`中设置`to`选项来实现：

  ```php
    'to' => [
        'address' => 'dev@domain.com',
        'name' => 'Dev Example'
    ],
  ```

- **Mailtrap**

  最后，你可以使用`Mailtrap`服务和`smtp`驱动发送邮件信息到`虚拟`邮箱，这种方法允许你在`Mailtrap`的消息查看器中查看最终的邮件。

--------------------------------------------------------------------------------

## 9.15.4 事件

Laravel 会发送邮件前触发一个事件，记住，这个事件是在邮件被发送时触发，而不是推送到队列时，你可以在 `EventServiceProvider`中注册事件监听器：

```php
/**
 * The event listener mappings for the application.
 *
 * @var array
 */
protected $listen = [
    'Illuminate\Mail\Events\MessageSending' => [
        'App\Listeners\LogSentMessage',
    ],
];
```

--------------------------------------------------------------------------------

# 9.16 包开发

## 9.16.1 简介

包是添加功能到`Laravel`的主要方式。包可以提供任何功能，小到处理日期如`Carbon`，大到整个`BDD`测试框架如 `Behat`。

当然，有很多不同类型的包。有些包是独立的，意味着可以在任何框架中使用，而不仅是`Laravel`。比如`Carbon` 和`Behat`都是独立的包。所有这些包都可以通过在`composer.json`文件中请求以便被`Laravel`使用。

另一方面，其它包只能特定和 Laravel 一起使用，这些包可能有路由，控制器、视图和配置用于加强 Laravel 应用的功能，本章主要讨论只能在 Laravel 中使用的包。

--------------------------------------------------------------------------------

## 9.16.2 服务提供者

服务提供者是包和`Laravel`之间的连接点。服务提供者负责绑定对象到`Laravel`的服务容器并告知`Laravel`从哪里加载包资源如视图、配置和本地化文件。

服务提供者继承自`Illuminate\Support\ServiceProvider`类并包含两个方法：`register`和`boot`。`ServiceProvider`基类位于`Composer`包`illuminate/support`。

要了解更多关于服务提供者的内容，查看其文档。

--------------------------------------------------------------------------------

## 9.16.3 路由

要定义包的路由，只需要在包服务提供者中的`boot`方法中引入路由文件。在路由文件中，可以使用`Route`门面注册路由，和`Laravel`应用中注册路由一样：

```php
/**
 * Perform post-registration booting of services.
 *
 * @return void
 */
public function boot(){
    if (! $this->app->routesAreCached()) {
        require __DIR__.'/../../routes.php';
    }
}
```

--------------------------------------------------------------------------------

## 9.16.4 资源

### 9.16.4.1 视图

要在`Laravel`中注册包视图，需要告诉`Laravel`视图在哪，可以使用服务提供者的`loadViewsFrom`方法来实现。`loadViewsFrom`方法接收两个参数：视图模板的路径和包名称。

例如，如果你的包名称是`courier`，添加如下代码到服务提供者的`boot`方法：

```php
/**
 * Perform post-registration booting of services.
 *
 * @return void
 */
public function boot(){
    $this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');
}
```

包视图通过使用类似的`package::view`语法来引用。所以，你可以通过如下方式加载`courier`包上的`admin`视图：

```php
Route::get('admin', function () {
    return view('courier::admin');
});
```

- **覆盖包视图**

  当你使用`loadViewsFrom`方法的时候，`Laravel`实际上为视图注册了两个存放位置：一个是`resources/views/vendor`目录，另一个是你指定的目录。

  所以，以courier为例：当请求一个包视图时，`Laravel`首先检查开发者是否在`resources/views/vendor/courier`提供了自定义版本的视图，如果该视图不存在，`Laravel`才会搜索你调用`loadViewsFrom`方法时指定的目录。这种机制使得终端用户可以轻松地自定义/覆盖包视图。

- **发布视图**

  如果你想要视图能够发布到应用的`resources/views/vendor`目录，可以使用服务提供者的`publishes`方法。该方法接收包视图路径及其相应的发布路径数组作为参数：

  ```php
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot(){
        $this->loadViewsFrom(__DIR__.'/path/to/views', 'courier');

        $this->publishes([
            __DIR__.'/path/to/views' => base_path('resources/views/vendor/courier'),
        ]);
    }
  ```

  现在，当包用户执行Laravel的`Artisan`命令`vendor:publish`时，你的视图包将会被拷贝到指定路径。

### 9.16.4.2 翻译

如果你的包包含翻译文件，你可以使用`loadTranslationsFrom`方法告诉`Laravel`如何加载它们。

例如，如果你的包命名为`courier`，你应该添加如下代码到服务提供者的`boot`方法：

```php
/**
 * Perform post-registration booting of services.
 *
 * @return void
 */
public function boot(){
    $this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');
}
```

包翻译使用形如`package::file.line`的语法进行引用。所以，你可以使用如下方式从`messages`文件中加载`courier`包的`welcome`行：

```php
echo trans('courier::messages.welcome');
```

- **发布翻译文件**

  如果你想要发布包翻译到应用的`resources/lang/vendor`目录，你可以使用服务提供者的`publishes`方法，该方法接收一个包路径和相应发布路径数组参数。

  例如，要发布`courier`包的翻译文件，可以这么做：

  ```php
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */public function boot(){
        $this->loadTranslationsFrom(__DIR__.'/path/to/translations', 'courier');

        $this->publishes([
            __DIR__.'/path/to/translations' => resource_path('lang/vendor/courier'),
        ]);}
  ```

  这样，包用户可以执行`Artisan`命令`vendor:publish`将包翻译文件发布到应用的指定目录。

### 9.16.4.3 配置

通常，你想要发布包配置文件到应用根目录下的`config`目录，这将允许包用户轻松覆盖默认配置选项，要发布一个配置文件，只需在服务提供者的`boot`方法中使用`publishes`方法即可：

```php
/**
 * Perform post-registration booting of services.
 *
 * @return void
 */
public function boot(){
    $this->publishes([
        __DIR__.'/path/to/config/courier.php' => config_path('courier.php'),
    ]);
}
```

现在，当包用户执行Laravel的`Artisan`命令`vendor:publish`时，你的文件将会被拷贝到指定位置，当然，配置被发布后，可以通过和其它配置选项一样的方式进行访问：

```php
$value = config('courier.option');
```

- **默认包配置**

  你还可以选择将自己的包配置文件合并到应用的拷贝版本，这允许用户只引入,他们在应用配置文件中实际要覆盖的配置选项。要合并两个配置，在服务提供者的`register`方法中使用`mergeConfigFrom`方法即可：

  ```php /**

  - Register bindings in the container. *
  - @return void */ public function register(){ $this->mergeConfigFrom(

    ```
     __DIR__.'/path/to/config/courier.php', 'courier'
    ```

     ); } ``

--------------------------------------------------------------------------------

## 9.16.5 前端资源

你的包可能包含`JavaScrip`t、`CSS`和`图片`，要发布这些前端资源到应用根目录下的`public`目录，使用服务提供者的`publishes`方法。

在本例中，我们添加一个前端资源组标签`public`，用于发布相关的前端资源组：

```php
/**
 * Perform post-registration booting of services.
 *
 * @return void
 */
public function boot(){
    $this->publishes([
        __DIR__.'/path/to/assets' => public_path('vendor/courier'),
    ], 'public');
}
```

现在，当包用户执行`vendor:publish`命令时，前端资源将会被拷贝到指定位置，由于你需要在每次包更新时重写前端资源，可以使用`--force`标识：

```
php artisan vendor:publish --tag=public --force
```

如果你想要确保前端资源已经更新到最新版本，可添加该命令到`composer.json`文件的`post-update-cmd`列表。

--------------------------------------------------------------------------------

## 9.16.6 发布文件组

你可能想要分开发布包前端资源组和资源。

例如，你可能想要用户发布包配置的同时不发布包前端资源，可以通过在调用时给它们打上`标签`来实现分离。下面我们在包服务提供者的`boot`方法中定义两个公共组：

```php
/**
 * Perform post-registration booting of services.
 *
 * @return void
 */
public function boot(){
    $this->publishes([
        __DIR__.'/../config/package.php' => config_path('package.php')
    ], 'config');

    $this->publishes([
        __DIR__.'/../database/migrations/' => database_path('migrations')
    ], 'migrations');
}
```

现在用户可以在使用Artisan命令`vendor:publish`时通过引用标签名来分开发布这两个组：

```php
php artisan vendor:publish --provider="Vendor\Providers\PackageServiceProvider" --tag="config"
```

--------------------------------------------------------------------------------
