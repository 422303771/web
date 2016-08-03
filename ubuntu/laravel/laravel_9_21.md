## 9.21 Envoy Task Runner

### 9.21.1 简介

`Laravel Envoy`为定义运行在远程主机上的通用任务提供了一套干净、最简化的语法。使用`Blade`样式语法，你可以轻松为开发设置任务，`Artisan`命令，以及更多，目前，`Envoy` 只支持`Mac`和`Linux`操作系统。

#### 9.21.1.1 安装

首先，使用`Composer`的global 命令安装`Envoy`：

```
composer global require "laravel/envoy=~1.0"
```
确保`~/.composer/vendor/bin`目录在系统路径`PATH`中否则在终端中由于找不到`envoy`而无法执行该命令。

* **更新Envoy**

还可以使用`Composer`保持安装的`Envoy`是最新版本：

```
composer global update
```

----

### 9.21.2 编写任务

所有的`Envoy`任务都定义在项目根目录下的`Envoy.blade.php`文件中，下面是一个让你开始的示例：

```php
@servers(['web' => 'user@192.168.1.1'])

@task('foo', ['on' => 'web'])
    ls -la
@endtask
```
正如你所看到的，`@servers`数组定义在文件顶部，从而允许你在任务声明中使用`on`选项引用这些服务器，在 `@task`声明中，应该放置将要在服务器上运行的`Bash`代码。

* **启动**

	有时候，你需要在评估`Envoy`任务之前执行一些`PHP`代码，可以在`Envoy`文件中使用`@setup`指令来声明变量和要执行的PHP代码：
	
	```php
	@setup
	    $now = new DateTime();
	    $environment = isset($env) ? $env : "testing";
	@endsetup
	```
	还可以使用`@include`来引入外部PHP文件：
	
	```
	@include('vendor/autoload.php');
	```

* **确认任务**

	如果你想要在服务器上运行给定任务之前弹出弹出提示进行确认，可以在任务声明中使用`confirm`指令：
	
	```php
	@task('deploy', ['on' => 'web', 'confirm' => true])
	    cd site
	    git pull origin {{ $branch }}
	    php artisan migrate
	@endtask
	```
	
#### 9.21.2.1 任务变量

如果需要的话，你可以使用命令行开关传递变量到`Envoy`文件，从而允许你自定义任务：

```
envoy run deploy --branch=master
```
你可以在任务中通过`Blade`的`echo`语法使用该选项：

```php
@servers(['web' => '192.168.1.1'])

@task('deploy', ['on' => 'web'])
    cd site
    git pull origin {{ $branch }}
    php artisan migrate
@endtask
```

#### 9.21.2.2 多个服务器

你可以轻松地在多主机上运行同一个任务，首先，添加额外服务器到`@servers`声明，每个服务器应该被指配一个唯一的名字。定义好服务器后，在任务声明中简单列出所有服务器即可：

```php
@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])

@task('deploy', ['on' => ['web-1', 'web-2']])
    cd site
    git pull origin {{ $branch }}
    php artisan migrate
@endtask
```
默认情况下，该任务将会依次在每个服务器上执行，这意味着，该任务在第一台服务器上运行完成后才会开始在第二台服务器运行。

* **平行运行**

	如果你想要在多个服务器上平行运行，添加`parallel`选项到任务声明：
	
	```php
	@servers(['web-1' => '192.168.1.1', 'web-2' => '192.168.1.2'])
	
	@task('deploy', ['on' => ['web-1', 'web-2'], 'parallel' => true])
	    cd site
	    git pull origin {{ $branch }}
	    php artisan migrate
	@endtask
	```

#### 9.21.2.3 任务宏

宏允许你使用单个命令中定义多个依次运行的任务。例如，`deploy`宏会运行`git`和`composer`任务：

```php
@servers(['web' => '192.168.1.1'])

@macro('deploy')
    git
    composer
@endmacro

@task('git')
    git pull origin master
@endtask

@task('composer')
    composer install
@endtask
```
宏被定义好了之后，你就可以通过如下单个命令运行它：

```
envoy run deploy
```


------

### 9.21.3 运行任务

要从`Envoy.blade.php`文件中运行一个任务，需要执行`Envoy`的`run`命令，然后传递你要执行的任务的命令名或宏。`Envoy`将会运行命令并从服务打印输出：

```
envoy run task
```

-----

### 9.21.4 通知

#### 9.21.4.1 HipChat

运行完一个任务后，可以使用`Envoy`的`@hipchat`指令发送通知到团队的`HipChat`房间，该指令接收一个`API`令牌、房间名称、和用户名：

```php
@servers(['web' => '192.168.1.1'])

@task('foo', ['on' => 'web'])
    ls -la
@endtask

@after
    @hipchat('token', 'room', 'Envoy')
@endafter
```
需要的话，你还可以传递自定义发送给`HipChat`房间的消息，所有在`Envoy`任务中有效的变量在构建消息时也有效：

```
@after
    @hipchat('token', 'room', 'Envoy', "{$task} ran in the {$env} environment.")
@endafter
```

#### 9.21.4.2 Slack

除了`HipChat`之外，`Envoy`还支持发送通知到`Slack`。`@slack`指令接收一个`Slack`钩子`URL`、频道名称、和你要发送给该频道的消息：

```
@after
    @slack('hook', 'channel', 'message')
@endafter
```
你可以通过创建集成到`Slack`网站的`Incoming WebHooks`来获取钩子URL，该`hook`参数是由`Incoming Webhooks Slack`集成提供的完整`webhook URL`，例如：

```
https://hooks.slack.com/services/ZZZZZZZZZ/YYYYYYYYY/XXXXXXXXXXXXXXX
```
你可以提供下面两种其中之一作为频道参数：

* 发送消息到频道:`#channel`

* 发送消息到用户:`@user`


----

## 9.22 任务调度

### 9.22.1 简介

在以前，开发者需要为每一个需要调度的任务编写一个`Cron`条目，这是很让人头疼的事。你的任务调度不在源码控制中，你必须使用`SSH登录`到服务器然后添加这些`Cron`条目。

Laravel命令调度器允许你平滑而又富有表现力地在Laravel中定义命令调度，并且服务器上只需要一个`Cron`条目即可。

任务调度定义在`app/Console/Kernel.php`文件的`schedule`方法中，该方法中已经包含了一个示例。你可以自由地添加你需要的调度任务到`Schedule`对象。

#### 9.22.1.1 开启调度

下面是你唯一需要添加到服务器的`Cron`条目：

```
* * * * * php /path/to/artisan schedule:run 1>> /dev/null 2>&1
```
该`Cron`将会每分钟调用Laravel命令调度，然后，Laravel评估你的调度任务并运行到期的任务。


----


### 9.22.2 定义调度

你可以在`App\Console\Kernel`类的`schedule`方法中定义所有调度任务。开始之前，让我们看一个调度任务的例子，在这个例子中，我们将会在每天午夜调度一个被调用的闭包。在这个闭包中我们将会执行一个数据库查询来清空表：

```php
<?php

namespace App\Console;

use DB;
use Illuminate\Console\Scheduling\Schedule;
use Illuminate\Foundation\Console\Kernel as ConsoleKernel;

class Kernel extends ConsoleKernel{
    /**
     * 应用提供的Artisan命令
     *
     * @var array
     */
    protected $commands = [
        'App\Console\Commands\Inspire',
    ];

    /**
     * 定义应用的命令调度
     *
     * @param  \Illuminate\Console\Scheduling\Schedule  $schedule
     * @return void
     */
    protected function schedule(Schedule $schedule)
    {
        $schedule->call(function () {
            DB::table('recent_users')->delete();
        })->daily();
    }
}
```
除了调度闭包调用外，还可以调度`Artisan`命令和`操作系统`命令。

例如，可以使用`command`方法来调度一个`Artisan`命令：

```
$schedule->command('emails:send --force')->daily();
```
`exec`命令可用于发送命令到操作系统：

```
$schedule->exec('node /home/forge/script.js')->daily();
```

#### 9.22.2.1 调度常用选项

当然，你可以分配多种调度到任务：

|方法|描述|
|:---:|:---|
|->cron('* * * * *');|在自定义Cron调度上运行任务|
|->everyMinute();|每分钟运行一次任务|
|->everyFiveMinutes();|每五分钟运行一次任务|
|->everyTenMinutes();|每十分钟运行一次任务|
|->everyThirtyMinutes();|每三十分钟运行一次任务|
|->hourly();|每小时运行一次任务|
|->daily();|每天凌晨零点运行任务|
|->dailyAt('13:00');|每天13:00运行任务|
|->twiceDaily(1, 13);|每天1:00 & 13:00运行任务|
|->weekly();|每周运行一次任务|
|->monthly();|每月运行一次任务|
|->quarterly();|每个季度运行一次|
|->yearly();|每年运行一次|

这些方法可以和额外的约束一起联合起来创建一周特定时间运行的更加细粒度的调度。

例如，要每周一调度一个命令：

```
$schedule->call(function () {
    // 每周星期一13:00运行一次...
})->weekly()->mondays()->at('13:00');
```
下面是额外的调度约束列表：

|方法|描述|
|:---:|:---|
|->weekdays();|只在工作日运行任务|
|->sundays();|每个星期天运行任务|
|->mondays();|每个星期一运行任务|
|->tuesdays();|每个星期二运行任务|
|->wednesdays();|每个星期三运行任务|
|->thursdays();|每个星期四运行任务|
|->fridays();|每个星期五运行任务|
|->saturdays();|每个星期六运行任务|
|->when(Closure);|基于特定测试运行任务|


* **基于测试的约束条件**

`when`方法用于限制任务在通过给定测试之后运行。换句话说，如果给定闭包返回`true`，只要没有其它约束条件阻止任务运行，该任务就会执行：

```
$schedule->command('emails:send')->daily()->when(function () {
    return true;
});
```
`reject`方法和`when`相反，如果`reject`方法返回`true`，调度任务将不会执行：

```
$schedule->command('emails:send')->daily()->reject(function () {
    return true;
});
```
使用`when`方法链的时候，调度命令将只会执行返回`true`的`when`方法。

#### 9.22.2.2 避免任务重叠

默认情况下，即使前一个任务仍然在运行调度任务也会运行，要避免这样的情况，可使用`withoutOverlapping`方法：

```
$schedule->command('emails:send')->withoutOverlapping();
```
在本例中，`Artisan`命令`emails:send`每分钟都会运行，如果该命令没有在运行的话。如果你的任务在执行时经常大幅度的变化，那么`withoutOverlapping`方法就非常有用，你不必再去预测给定任务到底要消耗多长时间。


----

### 9.22.3 任务输出

Laravel调度器为处理调度任务输出提供了多个方便的方法。首先，使用`sendOutputTo`方法，你可以发送输出到文件以便稍后检查：

```
$schedule->command('emails:send')
         ->daily()
         ->sendOutputTo($filePath);
```
如果你想要追加输出到给定文件，可以使用`appendOutputTo`方法：

```
$schedule->command('emails:send')
         ->daily()
         ->appendOutputTo($filePath);
```
使用`emailOutputTo`方法，你可以将输出发送到电子邮件，注意输出必须首先通过`sendOutputTo`方法发送到文件。还有，使用电子邮件发送任务输出之前，应该配置Laravel的电子邮件服务：

```
$schedule->command('foo')
         ->daily()
         ->sendOutputTo($filePath)
         ->emailOutputTo('foo@example.com');
```
*注意：`emailOutputTo`和`sendOutputTo`方法只对`command`方法有效，不支持`call`方法。*


-----

### 9.22.4 任务钩子

使用`before`和`after`方法，你可以指定在调度任务完成之前和之后要执行的代码：

```php
$schedule->command('emails:send')
         ->daily()
         ->before(function () {
             // Task is about to start...
         })
         ->after(function () {
             // Task is complete...
         });
```

* **ping URL**

	使用`pingBefore`和`thenPing`方法，调度器可以在任务完成之前和之后自动`ping`给定的URL。该方法在通知外部服务时很有用。
	
	例如`Laravel Envoyer`，在调度任务开始或完成的时候：
	
	```
	$schedule->command('emails:send')
	         ->daily()
	         ->pingBefore($url)
	         ->thenPing($url);
	```
	使用`pingBefore($url)`或`thenPing($url)`特性需要安装HTTP库`Guzzle`，可以在`composer.json`文件中添加如下行来安装`Guzzle`到项目：
	
	```
	"guzzlehttp/guzzle": "~5.3|~6.0"
	```
	
----