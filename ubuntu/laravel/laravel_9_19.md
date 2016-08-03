## 9.19 队列

### 9.19.1 简介

Laravel 队列服务为各种不同的后台队列提供了统一的`API`。队列允许你推迟耗时任务（例如发送邮件）的执行，从而大幅提高web请求速度。

#### 9.19.1.1 配置

队列配置文件存放在`config/queue.php`。在该文件中你将会找到框架自带的每一个队列驱动的连接配置，包括`数据库`、`Beanstalkd`、 `IronMQ`、`Amazon SQS`、 `Redis`以及`同步（本地使用）驱动`。其中还包含了一个`null队列驱动`以拒绝队列任务。

#### 9.19.1.2 队列驱动预备知识

* **数据库**

为了使用`database`队列驱动，需要一张`数据库表`来存放任务，要生成创建该表的迁移，运行`Artisan`命令`queue:table`，迁移被创建好了之后，使用`migrate`命令运行迁移：

```
php artisan queue:table
php artisan migrate
```

* **其它队列依赖**

下面是以上列出队列驱动需要安装的依赖：

* Amazon SQS:`aws/aws-sdk-php ~3.0`

* Beanstalkd:`pda/pheanstalk ~3.0`
	
* Redis:`predis/predis ~1.0`

-----

### 9.19.2 编写任务类

#### 9.19.2.1 生成任务类

默认情况下，应用的所有队列任务都存放在`app/Jobs`目录。你可以使用`Artisan CLI`生成新的队列任务：

```php
php artisan make:job SendReminderEmail
```
该命令将会在`app/Jobs`目录下生成一个新的类，并且该类实现了`Illuminate\Contracts\Queue\ShouldQueue`接口，告诉Laravel该任务应该被推送到队列而不是同步运行。

#### 9.19.2.2 任务类结构

任务类非常简单，正常情况下只包含一个当队列处理该任务时被执行的`handle`方法，让我们看一个任务类的例子：
```php
<?php

namespace App\Jobs;

use App\User;
use App\Jobs\Job;
use Illuminate\Contracts\Mail\Mailer;
use Illuminate\Queue\SerializesModels;
use Illuminate\Queue\InteractsWithQueue;
use Illuminate\Contracts\Queue\ShouldQueue;

class SendReminderEmail extends Job implements ShouldQueue
{
    use InteractsWithQueue, SerializesModels;

    protected $user;

    /**
     * 创建一个新的任务实例
     *
     * @param  User  $user
     * @return void
     */
    public function __construct(User $user)
    {
        $this->user = $user;
    }

    /**
     * 执行任务
     *
     * @param  Mailer  $mailer
     * @return void
     */
    public function handle(Mailer $mailer)
    {
        $mailer->send('emails.reminder', ['user' => $this->user], function ($m) {
            //
        });

        $this->user->reminders()->create(...);
    }
}
```
在本例中，注意我们能够直接将`Eloquent模型`传递到对列任务的构造函数中。由于该任务使用了`SerializesModels` trait，`Eloquent模型`将会在任务被执行时优雅地序列化和反序列化。

如果你的队列任务在构造函数中接收`Eloquent模型`，只有模型的主键会被序列化到队列，当任务真正被执行的时候，队列系统会自动从数据库中获取整个模型实例。

这对应用而言是完全透明的，从而避免序列化整个`Eloquent模型`实例引起的问题。

`handle`方法在任务被队列处理的时候被调用，注意我们可以在任务的`handle方法`中进行依赖注入。Laravel服务容器会自动注入这些依赖。

* **出错**

	如果任务被处理的时候抛出异常，则该任务将会被自动释放回队列以便再次尝试执行。任务会持续被释放直到尝试次数达到应用允许的最大次数。最大尝试次数通过`Artisan`任务`queue:listen`或`queue:work`上的`--tries`开关来定义。关于运行队列监听器的更多信息可以在下面看到。

* **手动释放任务**
	
	如果你想要手动释放任务，生成的任务类中自带的`InteractsWithQueue`trait提供了释放队列任务的`release`方法，该方法接收一个参数——同一个任务两次运行之间的等待时间：
	
	```php
	public function handle(Mailer $mailer){
	    if (condition) {
	        $this->release(10);
	    }
	}
	```

* **检查尝试运行次数**

	正如上面提到的，如果在任务处理期间发生异常，任务会自动释放回队列中，你可以通过`attempts`方法来检查该任务已经尝试运行次数：
	
	```php
	public function handle(Mailer $mailer){
	    if ($this->attempts() > 3) {
	        //
	    }
	}
	```

------

### 9.19.3 推送任务到队列

默认的 Laravel 控制器位于`app/Http/Controllers/Controller.php`并使用了`DispatchesJobs` trait。该trait提供了一些允许你方便推送任务到队列的方法，例如`dispatch`方法：

```php
<?php

namespace App\Http\Controllers;

use App\User;
use Illuminate\Http\Request;
use App\Jobs\SendReminderEmail;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 发送提醒邮件到指定用户
     *
     * @param  Request  $request
     * @param  int  $id
     * @return Response
     */
    public function sendReminderEmail(Request $request, $id)
    {
        $user = User::findOrFail($id);

        $this->dispatch(new SendReminderEmail($user));
    }
}
```

* **DispatchesJobs Trait**

	当然，有时候你想要从应用中路由或控制器之外的某些地方分发任务，因为这个原因，你可以在应用的任何类中包含`DispatchesJobs` trait，从而获取对分发方法的访问，举个例子，下面是使用该`trait`的示例类：
	
	```php
	<?php
	
	namespace App;
	
	use Illuminate\Foundation\Bus\DispatchesJobs;
	
	class ExampleClass{
	    use DispatchesJobs;
	}
	```

* **dispatch方法**

	或者，你也可以使用全局的`dispatch`方法：
	
	```php
	Route::get('/job', function () {
	    dispatch(new App\Jobs\PerformTask);
	    return 'Done!';
	});
	```
	
* **为任务指定队列**

	你还可以指定任务被发送到的队列。
	
	根据任务被推送到的不同队列，你可以对队列任务进行`分类`，甚至优先考虑分配给多个队列的`worker`数目。
	
	这并不会如队列配置文件中定义的那样将任务推送到不同队列`连接`，而只是在单个连接中发送给特定队列。要指定该队列，使用任务实例上的`onQueue`方法，该方法由 Laravel 自带的基类`App\Jobs\Job`中的 `Illuminate\Bus\Queueable` trait提供：
	
	```php
	<?php
	
	namespace App\Http\Controllers;
	
	use App\User;
	use Illuminate\Http\Request;
	use App\Jobs\SendReminderEmail;
	use App\Http\Controllers\Controller;
	
	class UserController extends Controller{
	    /**
	     * 发送提醒邮件到指定用户
	     *
	     * @param  Request  $request
	     * @param  int  $id
	     * @return Response
	     */
	    public function sendReminderEmail(Request $request, $id)
	    {
	        $user = User::findOrFail($id);
	        $job = (new SendReminderEmail($user))->onQueue('emails');
	        $this->dispatch($job);
	    }
	}
	```

#### 9.19.3.1 延迟任务

有时候你可能想要延迟队列任务的执行。例如，你可能想要将一个注册15分钟后给消费者发送提醒邮件的任务放到队列中，可以通过使用任务类上的`delay`方法来实现，该方法由`Illuminate\Bus\Queueable`trait提供：

```php
<?php

namespace App\Http\Controllers;

use App\User;
use Illuminate\Http\Request;
use App\Jobs\SendReminderEmail;
use App\Http\Controllers\Controller;

class UserController extends Controller{
    /**
     * 发送提醒邮件到指定用户
     *
     * @param  Request  $request
     * @param  int  $id
     * @return Response
     */
    public function sendReminderEmail(Request $request, $id)
    {
        $user = User::findOrFail($id);
        $job = (new SendReminderEmail($user))->delay(60);
        $this->dispatch($job);
    }
}
```
在本例中，我们指定任务在队列中开始执行前延迟60秒。

*注意：`Amazon SQS`服务最大延迟时间是`15`分钟。*

#### 9.19.3.2 任务事件

* **任务完成事件**

	`Queue::after`方法允许你在队列任务执行成功后注册一个要执行的回调函数。在该回调中我们可以添加日志、统计数据。
	
	例如，我们可以在Laravel内置的`AppServiceProvider`中添加事件回调:
	
	```php
	<?php
	
	namespace App\Providers;
	
	use Queue;
	use Illuminate\Support\ServiceProvider;
	
	class AppServiceProvider extends ServiceProvider
	{
	    /**
	     * Bootstrap any application services.
	     *
	     * @return void
	     */
	    public function boot()
	    {
	        Queue::after(function ($connection, $job, $data) {
	            //
	        });
	    }
	
	    /**
	     * Register the service provider.
	     *
	     * @return void
	     */
	    public function register()
	    {
	        //
	    }
	}
	```
------

### 9.19.4 运行队列监听器

* **启动任务监听器**

	Laravel 包含了一个`Artisan`命令用来运行被推送到队列的新任务。你可以使用`queue:listen`命令运行监听器：
	
	```
	php artisan queue:listen
	```
	还可以指定监听器使用哪个队列连接：
	
	```php
	php artisan queue:listen connection
	```
	注意一旦任务开始后，将会持续运行直到手动停止。你可以使用一个过程监视器如`Supervisor`来确保队列监听器没有停止运行。
	
* **队列优先级**

	你可以传递逗号分隔的队列连接列表到`listen`任务来设置队列优先级：
	
	```php
	php artisan queue:listen --queue=high,low
	```
	在本例中，`high`队列上的任务总是在从`low`队列移动任务之前被处理。

* **指定任务超时参数**

	你还可以设置每个任务允许运行的最大时间（以秒为单位）：
	
	```
	php artisan queue:listen --timeout=60
	```
	
* **指定队列睡眠时间**
	
	此外，可以指定轮询新任务之前的等待时间（以秒为单位）：
	
	```
	php artisan queue:listen --sleep=5
	```
	需要注意的是队列只会在队列上没有任务时“睡眠”，如果存在多个有效任务，该队列会持续运行，从不睡眠。

#### 9.19.4.1 Supervisor配置

`Supervisor`为Linux操作系统提供的进程监视器，将会在失败时自动重启`queue:listen`或`queue:work`命令，要在`Ubuntu`上安装`Supervisor`，使用如下命令：

```
sudo apt-get install supervisor
```
`Supervisor`配置文件通常存放在`/etc/supervisor/conf.d`目录，在该目录中，可以创建多个配置文件指示`Supervisor`如何监视进程。

例如，让我们创建一个开启并监视`queue:work`进程的`laravel-worker.conf`文件：

```
[program:laravel-worker]
process_name=%(program_name)s_%(process_num)02d
command=php /home/forge/app.com/artisan queue:work sqs --sleep=3 --tries=3 --daemon
autostart=true
autorestart=true
user=forge
numprocs=8
redirect_stderr=true
stdout_logfile=/home/forge/app.com/worker.log
```
在本例中，`numprocs`指令让`Supervisor`运行8个`queue:work`进程并监视它们，如果失败的话自动重启。配置文件创建好了之后，可以使用如下命令更新`Supervisor`配置并开启进程：

```
sudo supervisord -c /etc/supervisord.conf
sudo supervisorctl -c /etc/supervisor/supervisord.conf
sudo supervisorctl reread
sudo supervisorctl update
sudo supervisorctl start laravel-worker:*
```
要了解更多关于`Supervisor`的使用和配置，查看`[Supervisor文档](http://supervisord.org/index.html)`。此外，还可以使用`Laravel Forge`从`web接口`方便地自动配置和管理`Supervisor配置`。

#### 9.19.4.2 后台队列监听器

`Artisan`命令`queue:work`包含一个`--daemon`选项来强制队列`worker`持续处理任务而不必重新启动框架。相较于`queue:listen`命令该命令对CPU的使用有明显降低：

```php
php artisan queue:work connection --daemon
php artisan queue:work connection --daemon --sleep=3
php artisan queue:work connection --daemon --sleep=3 --tries=3
```
正如你所看到的，`queue:work`任务支持大多数`queue:listen`中有效的选项。你可以使用`php artisan help queue:work`任务来查看所有有效选项。

* **后台队列监听器编码考虑**

	后台队列`worker`在处理每个任务时不重启框架，因此，你要在任务完成之前释放资源，举个例子，如果你在使用GD库操作图片，那么就在完成时使用`imagedestroy`释放内存。
	
	类似的，数据库连接应该在后台长时间运行完成后断开，你可以使用`DB::reconnect`方法确保获取了一个新的连接。

#### 9.19.4.3 部署后台队列监听器

由于后台队列`worker`是常驻进程，不重启的话不会应用代码中的更改，所以，最简单的部署后台队列`worker`的方式是使用部署脚本重启所有`worker`，你可以通过在部署脚本中包含如下命令重启所有`worker`：

```
php artisan queue:restart
```
该命令会告诉所有队列`worker`在完成当前任务处理后重启以便没有任务被遗漏。

*注意：这个命令依赖于缓存系统重启进度表，默认情况下，`APC`在`CLI`任务中无法正常工作，如果你在使用`APC`，需要在`APC`配置中添加`apc.enable_cli=1`。*


-----

### 9.19.5 处理失败任务

由于事情并不总是按照计划发展，有时候你的队列任务会失败。别担心，它发生在我们大多数人身上！

Laravel包含了一个方便的方式来指定任务最大尝试执行次数，任务执行次数达到最大限制后，会被插入到`failed_jobs`表，失败任务的名字可以通过配置文件`config/queue.php`来配置。

要创建一个`failed_jobs`表的迁移，可以使用`queue:failed-table`命令：

```
php artisan queue:failed-table
```
运行队列监听器的时候，可以在`queue:listen`命令上使用`--tries`开关来指定任务最大可尝试执行次数：

```
php artisan queue:listen connection-name --tries=3
```

#### 9.19.5.1 失败任务事件

如果你想要注册一个队列任务失败时被调用的事件，可以使用`Queue::failing`方法，该事件通过`邮件`或`HipChat`通知团队。

举个例子，我么可以在Laravel自带的`AppServiceProvider`中附件一个回调到该事件：

```php
<?php

namespace App\Providers;

use Queue;
use Illuminate\Support\ServiceProvider;

class AppServiceProvider extends ServiceProvider{
    /**
     * 启动应用服务
     *
     * @return void
     */
    public function boot()
    {
        Queue::failing(function ($connection, $job, $data) {
            // Notify team of failing job...
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

* **任务类的失败方法**

	想要更加细粒度的控制，可以在队列任务类上直接定义`failed`方法，从而允许你在失败发生时执行指定动作：
	
	```php
	<?php
	
	namespace App\Jobs;
	
	use App\Jobs\Job;
	use Illuminate\Contracts\Mail\Mailer;
	use Illuminate\Queue\SerializesModels;
	use Illuminate\Queue\InteractsWithQueue;
	use Illuminate\Contracts\Bus\SelfHandling;
	use Illuminate\Contracts\Queue\ShouldQueue;
	
	class SendReminderEmail extends Job implements SelfHandling, ShouldQueue
	{
	    use InteractsWithQueue, SerializesModels;
	
	    /**
	     * 执行任务
	     *
	     * @param  Mailer  $mailer
	     * @return void
	     */
	    public function handle(Mailer $mailer)
	    {
	        //
	    }
	
	    /**
	     * 处理失败任务
	     *
	     * @return void
	     */
	    public function failed()
	    {
	        // Called when the job is failing...
	    }
	}
	```

#### 9.19.5.2 重试失败任务

要查看已插入到`failed_jobs`数据表中的所有失败任务，可以使用Artisan命令`queue:failed`：

```
php artisan queue:failed
```
该命令将会列出`任务ID`，连接，对列和失败时间，`任务ID`可用于重试失败任务。

例如，要重试一个ID为5的失败任务，要用到下面的命令：

```
php artisan queue:retry 5
```
要重试所有失败任务，使用如下命令即可：

```
php artisan queue:retry all
```
如果你要删除一个失败任务，可以使用`queue:forget`命令：

```
php artisan queue:forget 5
```
要删除所有失败任务，可以使用`queue:flush`命令：

```
php artisan queue:flush
```

----

## 9.20 Session

### 9.20.1 简介

由`于HTTP驱动`的应用是无状态的，所以我们使用`Session`来存储用户请求信息。Laravel通过干净、统一的`API`处理后端各种`Session`驱动，目前支持的流行后端驱动包括`Memcached`、`Redis`和`数据库`。

#### 9.20.1.1 配置

`Session`配置文件位于`config/session.php`。默认情况下，Laravel使用的`session`驱动为文件驱动，这对许多应用而言是没有什么问题的。在生产环境中，你可能考虑使用`memcached`或者`redis`驱动以便获取更快的`session`性能。

`session`驱动定义请求的`Session`数据存放在哪里，Laravel可以处理多种类型的驱动：

* `file` – session数据存储在`storage/framework/sessions`目录下；
* `cookie` – session数据存储在经过加密的安全的cookie中；
* `database` – session数据存储在数据库中；
* `memcached`/`redis` – session数据存储在`memcached/redis`中；
* `array` – session数据存储在简单PHP数组中，在多个请求之间是非持久化的。

*注意：数组驱动通常用于运行测试以避免`session`数据持久化。*

#### 9.20.1.2 Session驱动预备知识

* **数据库**

	当使用`databasesession`驱动时，需要设置表包含`session`项，下面是该数据表的表结构声明：
	
	```
	Schema::create('sessions', function ($table) {
	    $table->string('id')->unique();
	    $table->text('payload');
	    $table->integer('last_activity');
	});
	```
	你可以使用Artisan命令`session:table`来生成迁移：
	
	```
	php artisan session:table
	composer dump-autoload
	php artisan migrate
	```

* **Redis**

在Laravel中使用`Redis session`驱动前，需要通过`Composer`安装`predis/predis`包。

#### 9.20.1.3  其它Session相关问题

Laravel框架内部使用`flash session`键，所以你不应该通过该名称添加数据项到`session`。

如果你需要所有存储的`session`数据经过加密，在配置文件中设置`encrypt`配置为`true`。

------

### 9.20.2 基本使用

* **访问session**

	首先，我们来访问`session`，我们可以通过HTTP请求访问`session`实例，可以在控制器方法中通过类型提示引入请求实例，记住，控制器方法依赖通过Laravel服务容器注入：
	
	```php
	<?php
	
	namespace App\Http\Controllers;
	
	use Illuminate\Http\Request;
	use App\Http\Controllers\Controller;
	
	class UserController extends Controller{
	    /**
	     * 显示指定用户的属性
	     *
	     * @param  Request  $request
	     * @param  int  $id
	     * @return Response
	     */
	    public function showProfile(Request $request, $id)
	    {
	        $value = $request->session()->get('key');
	
	        //
	    }
	}
	```
	从`session`中获取数据的时候，还可以传递默认值作为第二个参数到`get`方法，默认值在指定键在`session`中不存在时返回。如果你传递一个闭包作为默认值到`get`方法，该闭包会执行并返回执行结果：
	
	```
	$value = $request->session()->get('key', 'default');
	
	$value = $request->session()->get('key', function() {
	    return 'default';
	});
	```
	如果你想要从`session`中获取所有数据，可以使用`all`方法：
	
	```
	$data = $request->session()->all();
	```
	还可以使用全局的PHP函数`session`来获取和存储`session`中的数据：
	
	```php
	Route::get('home', function () {
	    // 从session中获取数据...
	    $value = session('key');
	
	    // 存储数据到session...
	    session(['key' => 'value']);
	});
	```

* **判断session中是否存在指定项**
	
	`has`方法可用于检查数据项在`session`中是否存在。如果存在的话返回`true`：
	
	```
	if ($request->session()->has('users')) {
	    //
	}
	```
	
* **在session中存储数据**

	获取到`session`实例后，就可以调用多个方法来与底层数据进行交互。
	
	例如，`put`方法存储新的数据到`session`中：
	
	```
	$request->session()->put('key', 'value');
	```

* **推送数据到数组session**

	`push`方法可用于推送数据到值为数组的`session`。
	
	例如，如果·user.teams·键包含团队名数组，可以像这样推送新值到该数组：
	
	```php
	$request->session()->push('user.teams', 'developers');
	```

* **获取并删除数据**

	`pull`方法将会从`session`获取并删除数据：
	
	```php
	$value = $request->session()->pull('key', 'default');
	```

* **从session中删除数据项**

	`forget`方法从`session`中移除指定数据，如果你想要从`session`中移除所有数据，可以使用`flush`方法：
	
	```php
	$request->session()->forget('key');
	$request->session()->flush();
	```

* **重新生成Session ID**

	如果你需要重新生成`session` ID，可以使用`regenerate`方法：
	
	```
	$request->session()->regenerate();
	```

#### 9.20.2.1 一次性数据

有时候你可能想要在`session`中存储只在下个请求中有效的数据，可以通过`flash`方法来实现。

使用该方法存储的`session`数据只在随后的HTTP请求中有效，然后将会被删除：

```php
$request->session()->flash('status', 'Task was successful!');
```
如果你需要在更多请求中保持该一次性数据，可以使用`reflash`方法，该方法将所有一次性数据保留到下一个请求，如果你只是想要保存特定一次性数据，可以使用`keep`方法：

```php
$request->session()->reflash();
$request->session()->keep(['username', 'email']);
```

-----

### 9.20.3 添加自定义Session驱动

要为Laravel后端`session`添加更多驱动，可以使用`Session`门面上的`extend`方法。可以在服务提供者的`boot`方法中调用该方法：

```php
<?php

namespace App\Providers;

use Session;
use App\Extensions\MongoSessionStore;
use Illuminate\Support\ServiceProvider;

class SessionServiceProvider extends ServiceProvider{
    /**
     * Perform post-registration booting of services.
     *
     * @return void
     */
    public function boot()
    {
        Session::extend('mongo', function($app) {
            // Return implementation of SessionHandlerInterface...
            return new MongoSessionStore;
        });
    }

    /**
     * Register bindings in the container.
     *
     * @return void
     */
    public function register()
    {
        //
    }
}
```
需要注意的是自定义`session`驱动需要实现`SessionHandlerInterface`接口，该接口包含少许我们需要实现的方法，一个`MongoDB`的实现如下：

```php
<?php

namespace App\Extensions;

class MongoHandler implements SessionHandlerInterface{
    public function open($savePath, $sessionName) {}
    public function close() {}
    public function read($sessionId) {}
    public function write($sessionId, $data) {}
    public function destroy($sessionId) {}
    public function gc($lifetime) {}
}
```
由于这些方法并不像缓存的`StoreInterface`接口方法那样容易理解，我们接下来快速过一遍每一个方法：

* `open`方法用于基于文件的`session`存储系统，由于Laravel已经有了一个`file session`驱动，所以在该方法中不需要放置任何代码，可以将其置为空方法。

* `close`方法和`open`方法一样，也可以被忽略，对大多数驱动而言都用不到该方法。

* `read`方法应该返回与给定`$sessionId`相匹配的`session`数据的字符串版本，从驱动中获取或存储`session`数据不需要做任何序列化或其它编码，因为Laravel已经为我们做了序列化。

* `write`方法应该讲给定`$data`写到持久化存储系统相应的`$sessionId` , 例如`MongoDB`,`Dynamo`等等。

* `destroy`方法从持久化存储中移除`$sessionId`对应的数据。

* `gc`方法销毁大于给定`$lifetime`的所有`session`数据，对本身拥有过期机制的系统如`Memcached`和`Redis`而言，该方法可以留空。

`session`驱动被注册之后，就可以在配置文件`config/session.php`中使用`mongo`驱动了。


-----