<!--toc-->

- [9.3 Artisan Console](#93-artisan-console)
	- [9.3.1 简介](#931-简介)
	- [9.3.2 编写命令](#932-编写命令)
		- [9.3.2.1 命令结构](#9321-命令结构)
	- [9.3.3 命令I/O](#933-命令io)
		- [9.3.3.1 定义期望输入](#9331-定义期望输入)
		- [9.3.3.2 获取输入](#9332-获取输入)
		- [9.3.3.3 输入提示](#9333-输入提示)
		- [9.3.3.4 编写输出](#9334-编写输出)
	- [9.3.4 注册命令](#934-注册命令)
	- [9.3.5 通过代码调用命令](#935-通过代码调用命令)
		- [9.3.5.1 通过其他命令调用命令](#9351-通过其他命令调用命令)
- [9.4 订阅支付实现：Laravel Cashier](#94-订阅支付实现laravel-cashier)
	- [9.4.1 简介](#941-简介)
		- [9.4.1.1 安装&配置](#9411-安装配置)
	- [9.4.2 订阅实现](#942-订阅实现)
		- [9.4.2.1 创建订阅](#9421-创建订阅)
		- [9.4.2.2 检查订阅状态](#9422-检查订阅状态)
		- [9.4.2.3 修改订阅](#9423-修改订阅)
		- [9.4.2.4 订阅数量](#9424-订阅数量)
		- [9.4.2.5 订阅税金](#9425-订阅税金)
		- [9.4.2.6 取消订阅](#9426-取消订阅)
		- [9.4.2.7 恢复订阅](#9427-恢复订阅)
	- [9.4.3 处理Stripe Webhook](#943-处理stripe-webhook)
		- [9.4.3.1 订阅失败处理](#9431-订阅失败处理)
		- [9.4.3.2 其它Webhooks](#9432-其它webhooks)
	- [9.4.4 一次性付款](#944-一次性付款)
	- [9.4.5 发票](#945-发票)
		- [9.4.5.1 生成PDF发票](#9451-生成pdf发票)

<!-- tocstop -->

-----

# 9.3 Artisan Console

## 9.3.1 简介

`Artisan` 是 Laravel 自带的命令行接口名称，它为我们在开发过程中提供了很多有用的命令。通过强大的 `Symfony Console` 组件驱动。想要查看所有可用的`Artisan`命令，可使用`list`命令：

```
php artisan list
```

每个命令都可以用`help`指令显示命令描述及命令参数和选项。想要查看帮助界面，只需要在命令前加上`help`就可以了：

```
php artisan help migrate
```

--------------------------------------------------------------------------------

## 9.3.2 编写命令

除了`Artisan`提供的命令之外，还可以构建自己的命令。你可以将自定义命令存放在`app/Console/Commands`目录；当然，你可以自己选择存放位置，只要该命令可以被`composer.json`自动加载即可。

要创建一个新命令，你可以使用`Artisan`命令`make:console`：

```
php artisan make:console SendEmails
```

上述命令将会生成一个类`app/Console/Commands/SendEmails.php`，当创建命令时，`--command`选项可用于分配终端命令名（在终端调用命令时用）：

```
php artisan make:console SendEmails --command=emails:send
```

### 9.3.2.1 命令结构

命令生成以后，需要填写该类的`signature`和`description`属性，这两个属性在调用`list`显示命令的时候会被用到。

`handle`方法在命令执行时被调用，你可以将所有命令逻辑都放在这个方法里面，让我们先看一个命令例子。

我们可以在命令控制器的构造函数中注入任何依赖，Laravel 服务提供者将会在构造函数中自动注入所有依赖类型提示。要增强代码的复用性，保持代码轻量级并让它们延迟到应用服务中完成任务是个不错的实践：

```php
<?php

namespace App\Console\Commands;

use App\User;
use App\DripEmailer;
use Illuminate\Console\Command;
use Illuminate\Foundation\Inspiring;

class Inspire extends Command{
    /**
     * 控制台命令名称
     *
     * @var string
     */
    protected $signature = 'email:send {user}';

    /**
     * 控制台命令描述
     *
     * @var string
     */
    protected $description = 'Send drip e-mails to a user';

    /**
     * The drip e-mail service.
     *
     * @var DripEmailer
     */
    protected $drip;

    /**
     * 创建新的命令实例
     *
     * @param  DripEmailer  $drip
     * @return void
     */
    public function __construct(DripEmailer $drip)
    {
        parent::__construct();
        $this->drip = $drip;
    }

    /**
     * 执行控制台命令
     *
     * @return mixed
     */
    public function handle()
    {
        $this->drip->send(User::find($this->argument('user')));
    }
}
```

--------------------------------------------------------------------------------

## 9.3.3 命令I/O

### 9.3.3.1 定义期望输入

编写控制台命令的时候，通常通过参数和选项收集用户输入，Laravel使这项操作变得很方便：在命令中使用`signature`属性来定义我们期望的用户输入。`signature`属性通过一个优雅的、路由风格的语法允许你定义命令的名称、参数以及选项。所有用户提供的参数和选项都包含在大括号里，下面这个例子定义的命令要求用户输入必选参数`user`：

```php
/**
 * 控制台命令名称
 *
 * @var string
 */
protected $signature = 'email:send {user}';
```

你还可以让该参数可选可不选，以及定义默认的可选参数值：

```php
// 选项参数...
email:send {user?}
// 带默认值的选项参数...
email:send {user=foo}
```

选项，和参数一样，也是用户输入的一种格式，不同之处在于选项前面有两个短划线（–），我们可以这样定义选项：

```
/**
 * 控制台命令名称
 *
 * @var string
 */
protected $signature = 'email:send {user} {--queue}';
```

在本例中，`--queue`开关在调用`Artisan`命令的时候被指定。如果`--queue`开关被传递，其值是`true`，否则其值是`false`：

```
php artisan email:send 1 --queue
```

你还可以指定选项值被用户通过`=`来分配：

```
/**
 * 控制台命令名称
 *
 * @var string
 */
protected $signature = 'email:send {user} {--queue=}';
```

在本例中，用户可以通过这样的方式传值：

```
php artisan email:send 1 --queue=default
```

还可以给选项分配默认值：

```
email:send {user} {--queue=default}
```

如果想要为命令选项分配一个简写，可以在选项前指定并使用分隔符|将简写和完整选项名分开：

```
email:send {user} {--Q|queue}
```

如果你想要定义参数和选项以便指定输入数组，可以使用字符`*`：

```
email:send {user*}
email:send {user} {--id=*}
```

- **输入描述**

  你可以通过`:`将参数和描述进行分隔的方式,分配描述到输入参数和选项：

  ```
    /**
     * 控制台命令名称
     *
     * @var string
     */
    protected $signature = 'email:send
        {user : The ID of the user}
        {--queue= : Whether the job should be queued}';
  ```

### 9.3.3.2 获取输入

在命令被执行的时候，很明显，你需要访问命令获取的参数和选项的值。使用`argument`和`option`方法即可实现：

要获取参数的值，通过`argument`方法：

```
/**
 * 执行控制台命令
 *
 * @return mixed
 */
public function handle(){
    $userId = $this->argument('user');
}
```

如果你需要以数组形式获取所有参数值，使用不带参数的`argument`：

```
$arguments = $this->argument();
```

选项值和参数值的获取一样简单，使用`option`方法，同`argument`一样如果要获取所有选项值，可以调用不带参数的`option`方法：

```
// 获取指定选项...
$queueName = $this->option('queue');
// 获取所有选项...
$options = $this->option();
```

如果参数或选项不存在，返回`null`。

### 9.3.3.3 输入提示

除了显示输出之外，你可能在命令执行期间要用户提供输入。`ask`方法将会使用给定问题,提示用户，接收输入，然后返回用户输入到命令：

```
/**
 * 执行控制台命令
 *
 * @return mixed
 */
public function handle(){
    $name = $this->ask('What is your name?');
}
```

`secret`方法和`ask`方法类似，但用户输入在终端对他们而言是不可见的，这个方法在问用户一些敏感信息如密码时很有用：

```
$password = $this->secret('What is the password?');
```

- **让用户确认**

  如果你需要让用户确认信息，可以使用`confirm`方法，默认情况下，该方法返回`false`，如果用户输入`y`，则该方法返回`true`：

  ```
    if ($this->confirm('Do you wish to continue? [y|N]')) {
        //
    }
  ```

- **给用户提供选择**

  `anticipate`方法可用于为可能的选项提供自动完成功能，用户仍然可以选择答案，而不管这些选择：

  ```
    $name = $this->anticipate('What is your name?', ['Taylor', 'Dayle']);
  ```

  如果你需要给用户预定义的选择，可以使用`choice`方法。用户选择答案的索引，但是返回给你的是答案的值。如果用户什么都没选的话你可以设置默认返回的值：

  ```
    $name = $this->choice('What is your name?', ['Taylor', 'Dayle'], false);
  ```

### 9.3.3.4 编写输出

要将输出发送到控制台，使用`line`,`info`,`comment`,`question`和`error`方法，每个方法都会使用相应的ANSI颜色以作标识。

要显示一条信息消息给用户，使用`info`方法。通常，在终端显示为绿色：

```
/**
 * 执行控制台命令
 *
 * @return mixed
 */
public function handle(){
    $this->info('Display this on the screen');
}
```

要显示一条错误消息，使用`error`方法。错误消息文本通常是红色：

```
$this->error('Something went wrong!');
```

如果你想要显示原生输出，可以使用`line`方法，该方法输出的字符不带颜色：

```
$this->line('Display this on the screen');
```

- **表格布局**

  `table`方法使输出多行/列格式的数据变得简单，只需要将头和行传递给该方法，宽度和高度将基于给定数据自动计算：

  ```php
    $headers = ['Name', 'Email'];
    $users = App\User::all(['name', 'email'])->toArray();
    $this->table($headers, $users);
  ```

- **进度条**

  对需要较长时间运行的任务，显示进度指示器很有用，使用该输出对象，我们可以开始、前进以及停止该进度条。在开始进度时你必须定义步数，然后每走一步进度条前进一格：

  ```php
    $users = App\User::all();

    $this->output->progressStart(count($users));

    foreach ($users as $user) {
        $this->performTask($user);
        $this->output->progressAdvance();
    }

    $this->output->progressFinish();
  ```

  想要了解更多，查看[Symfony进度条组件文档](http://symfony.com/doc/2.7/components/console/helpers/progressbar.html)。

--------------------------------------------------------------------------------

## 9.3.4 注册命令

命令编写完成后，需要注册到`Artisan`才可以使用，这可以在`app/Console/Kernel.php`文件中完成。

在该文件中，你会在`commands`属性中看到一个命令列表，要注册你的命令，只需将其加到该列表中即可。当`Artisan`启动的时候，该属性中列出的命令将会被服务容器解析被注册到`Artisan`：

```php
protected $commands = [
    'App\Console\Commands\SendEmails'
];
```

## 9.3.5 通过代码调用命令

有时候你可能希望在`CLI`之外执行`Artisan`命令，比如，你可能希望在路由或控制器中触发`Artisan`命令，你可以使用`Artisan`门面上的`call`方法来完成这个。`call`方法接收被执行的命令名称作为第一个参数，命令参数数组作为第二个参数，退出代码被返回：

```php
Route::get('/foo', function () {
    $exitCode = Artisan::call('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);
});
```

使用`Artisan`上的`queue`方法，你甚至可以将`Artisan`命令放到队列中，这样它们就可以通过后台的队列工作者来处理：

```php
Route::get('/foo', function () {
    Artisan::queue('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);
});
```

如果你需要指定不接收字符串的选项值，例如`migrate:refresh`命令上的`--force`标识，可以传递布尔值`true`或`false`：

```
$exitCode = Artisan::call('migrate:refresh', [
    '--force' => true,
]);
```

### 9.3.5.1 通过其他命令调用命令

有时候你希望从一个已存在的`Artisan`命令中调用其它命令。你可以通过使用`call`方法开实现这一目的。`call`方法接收命令名称和数组形式的命令参数：

```php
/**
 * 执行控制台命令
 *
 * @return mixed
 */
public function handle(){
    $this->call('email:send', [
        'user' => 1, '--queue' => 'default'
    ]);
}
```

如果你想要调用其它控制台命令并阻止其所有输出，可以使用`callSilent`方法。`callSilent`方法和`call`方法用法一致：

```
$this->callSilent('email:send', [
    'user' => 1, '--queue' => 'default'
]);
```

--------------------------------------------------------------------------------

# 9.4 订阅支付实现：Laravel Cashier

## 9.4.1 简介

Laravel Cashier 为通过`Stripe`实现订阅支付服务提供了一个优雅平滑的接口。它封装了几乎所有你恐惧编写的样板化的订阅支付代码。除了基本的`订阅管理外`，`Cashier`还支持`处理优惠券`、`订阅升级/替换`、`订阅数量`、`取消宽限期`，甚至`生成PDF发票`。

### 9.4.1.1 安装&配置

- **Composer**

  首先，添加`Cashier`包到`composer.json`文件并运行`composer update`命令：

  ```
    "laravel/cashier": "~6.0"
  ```

- **服务提供者**

  接下来，在`config/app.php`配置文件中注册服务提供者：`Laravel\Cashier\CashierServiceProvider`。

- **迁移**

  使用`Cashier`之前，我们需要准备好数据库。我们需要添加一个字段到`users`表，还要创建新的 `subscriptions`表来处理所有用户订阅：

  ```php
    Schema::table('users', function ($table) {
        $table->string('stripe_id')->nullable();
        $table->string('card_brand')->nullable();
        $table->string('card_last_four')->nullable();
    });

    Schema::create('subscriptions', function ($table) {
        $table->increments('id');
        $table->integer('user_id');
        $table->string('name');
        $table->string('stripe_id');
        $table->string('stripe_plan');
        $table->integer('quantity');
        $table->timestamp('trial_ends_at')->nullable();
        $table->timestamp('ends_at')->nullable();
        $table->timestamps();
    });
  ```

  创建好迁移后，只需简单运行`migrate`命令，相应修改就会更新到数据库。

- **设置模型**

  接下来，添加`Billable trait`到`User`模型类：

  ```php
    use Laravel\Cashier\Billable;

    class User extends Authenticatable{
        use Billable;
    }
  ```

- **Stripe 键**

  最后，在配置文件`config/services.php`中设置`Stripe`键：

  ```
    'stripe' => [
        'model'  => 'User',
        'secret' => env('STRIPE_API_SECRET'),
    ],
  ```

--------------------------------------------------------------------------------

## 9.4.2 订阅实现

### 9.4.2.1 创建订阅

要创建一个订阅，首先要获取一个账单模型的实例，通常是`App\User`的实例。获取到该模型实例之后，你可以使用`newSubscription`方法来创建该模型的订阅：

```
$user = User::find(1);
$user->newSubscription('main', 'monthly')->create($creditCardToken);
```

第一个传递给`newSubscription`方法的参数是该订阅的名字，如果应用只有一个订阅，可以将其称作 `main`或`primary`，第二个参数用于指定用户订阅的`Stripe`计划，该值对应`Stripe`中相应计划的 `id`。

`create`方法会自动创建这个`Stripe`订阅，同时更新数据库中`Stripe`的客户`ID`（即`users`表中的`stripe_id`）和其它相关的账单信息。如果你的订阅计划有试用期，试用期结束时间也会自动被设置到数据库相应字段。

- **额外的用户信息**

  如果你想要指定额外的客户信息，你可以将其作为第二个参数传递给`create`方法：

  ```php
    $user->newSubscription('main', 'monthly')->create($creditCardToken, [
        'email' => $email,
        'description' => 'Our First Customer'
    ]);
  ```

  要了解更多`Stripe`支持的字段，可以查看`Stripe`关于[创建消费者文档](https://stripe.com/docs/api#create_customer)。

- **优惠券**

  如果你想要在创建订阅的时候使用优惠券，可以使用`withCoupon`方法：

  ```
    $user->newSubscription('main', 'monthly')
         ->withCoupon('code')
         ->create($creditCardToken);
  ```

### 9.4.2.2 检查订阅状态

用户订阅你的应用后，你可以使用各种便利的方法来简单检查订阅状态。首先，如果用户有一个有效的订阅，则`subscribed`方法返回`true`，即使订阅现在出于试用期：

```
if ($user->subscribed('main')) {
    //
}
```

`subscribed`方法还可以用于路由中间件，基于用户订阅状态允许你对路由和控制器的访问进行过滤：

```
public function handle($request, Closure $next){
    if ($request->user() && ! $request->user()->subscribed('main')) {
        // This user is not a paying customer...
        return redirect('billing');
    }

    return $next($request);
}
```

如果你想要判断一个用户是否还在试用期，可以使用`onTrial`方法,该方法对于，还处于试用期的用户显示警告信息很有用：

```
if ($user->->subscription('main')->onTrial()) {
    //
}
```

`onPlan`方法可用于判断用户是否基于`Stripe ID`订阅了给定的计划：

```
if ($user->onPlan('monthly')) {
    //
}
```

- **已取消的订阅状态**

  要判断用户是否曾经是有效的订阅者，但现在取消了订阅，可以使用`cancelled`方法：

  ```
    if ($user->subscription('main')->cancelled()) {
        //
    }
  ```

  你还可以判断用户是否曾经取消过订阅，但现在仍然在"宽限期"直到完全失效。例如，如果一个用户在3月5号取消了一个实际有效期到3月10号的订阅，该用户处于"宽限期"直到3月10号。注意 `subscribed`方法在此期间仍然返回`true`。

  ```php
    if ($user->subscription('main')->onGracePeriod()) {
        //
    }
  ```

### 9.4.2.3 修改订阅

用户订阅应用后，偶尔想要改变到新的订阅计划，要将用户切换到新的订阅，使用`swap`方法。例如，我们可以轻松切换用户到`premium`订阅：

```php
$user = App\User::find(1);
$user->subscription('main')->swap('stripe-plan-id');
```

如果用户在试用，试用期将会被维护。还有，如果订阅存在数量，数量也可以被维护。切换订阅计划后， 可以使用`invoice`方法立即给用户开发票：

```php
$user->subscription('main')->swap('stripe-plan-id');$user->invoice();
```

### 9.4.2.4 订阅数量

有时候订阅也会被数量影响，例如，应用中每个账户每月需要付费$10，要简单增加或减少订阅数量，使用`incrementQuantity`和`decrementQuantity`方法：

```
$user = User::find(1);

$user->subscription('main')->incrementQuantity();

// Add five to the subscription's current quantity...
$user->subscription('main')->incrementQuantity(5);

$user->subscription('main')->decrementQuantity();

// Subtract five to the subscription's current quantity...
$user->subscription('main')->decrementQuantity(5);
```

你也可以使用`updateQuantity`方法指定数量：

```php
$user->subscription('main')->updateQuantity(10);
```

想要了解更多订阅数量信息，查阅相关[Stripe文档](https://stripe.com/docs/guides/subscriptions#setting-quantities)。

### 9.4.2.5 订阅税金

在`Cashier`中，提供`tax_percent`值发送给`Stripe`很简单。要指定用户支付订阅的税率，实现账单模型的`getTaxPercent`方法，并返回一个在`0`到`100`之间的数值，不要超过两位小数：

```php
public function getTaxPercent() {
    return 20;
}
```

这将使你可以在模型基础上使用税率，对跨越不同国家的用户很有用。

### 9.4.2.6 取消订阅

要取消订阅，可以调用用户订阅上的`cancel`方法：

```
$user->subscription('main')->cancel();
```

当订阅被取消时，`Cashier`将会自动设置数据库中的`subscription_ends_at`字段。该字段用于了解 `subscribed`方法什么时候开始返回`false`。

例如，如果客户3月1号份取消订阅，但订阅直到3月5号才会结束，那么`subscribed`方法继续返回 `true`直到3月5号。

你可以使用 `onGracePeriod`方法判断用户是否已经取消订阅但仍然在"宽限期"：

```
if ($user->subscription('main')->onGracePeriod()) {
    //
}
```

### 9.4.2.7 恢复订阅

如果用户已经取消订阅但想要恢复该订阅，可以使用`resume`方法，前提是该用户必须在宽限期内：

```
$user->subscription('main')->resume();
```

如果该用户取消了一个订阅然后在订阅失效之前恢复了这个订阅，则不会立即支付该账单，取而代之的，他们的订阅只是被重新激活，并回到正常的支付周期。

可以在类中进行判断。编写自定义判断语句。

--------------------------------------------------------------------------------

## 9.4.3 处理Stripe Webhook

### 9.4.3.1 订阅失败处理

如果客户的信用卡失效怎么办？不用担心---- `Cashier`自带了`Webhook`控制器，该控制器可以很方便地为你取消客户订阅。只需要定义如下控制器路由：

```php
Route::post('stripe/webhook', 'Laravel\Cashier\WebhookController@handleWebhook');
```

就是这样！失败的支付将会被该控制器捕获和处理。当`Stripe`判断订阅失败（正常情况下尝试支付失败三次后）时该控制器将会取消客户的订阅。

**不要忘了：你需要在`Stripe`控制面板设置中配置相应的`webhook URI`，否则不能正常工作。**

由于`Stripe webhooks`需要通过`Laravel`的`CSRF`验证，所以我们将该`URI`置于`VerifyCsrfToken` 中间件排除列表中：

```php
protected $except = [
    'stripe/*',
];
```

### 9.4.3.2 其它Webhooks

如果你有额外想要处理的`Stripe webhook`事件，只需简单继承`Webhook`控制器， 你的方法名应该和 `Cashier`期望的约定一致，尤其是方法应该以`handle`开头并以驼峰命名法命名。

例如，如果你想要处理`invoice.payment_succeeded` webhook，你应该添加 `handleInvoicePaymentSucceeded`方法到控制器：

```php
<?php

namespace App\Http\Controller;

use Laravel\Cashier\WebhookController as BaseController;

class WebhookController extends BaseController{
    /**
     * 处理 stripe webhook.
     *
     * @param  array  $payload
     * @return Response
     */
    public function handleInvoicePaymentSucceeded($payload)
    {
        // 处理该事件
    }
}
```

--------------------------------------------------------------------------------

## 9.4.4 一次性付款

如果你想要使用订阅客户的信用卡一次性结清账单，可以使用账单模型实例上的`charge`方法，该方法接收付款金额（应用使用的货币的最小单位对应的金额数值）作为参数。

例如，下面的例子使用信用卡支付100美分，或1美元：

```
$user->charge(100);
```

`charge`方法接收一个数组作为第二个参数，允许你传递任何你想要传递的底层`Stripe`账单创建参数：

```php
$user->charge(100, [
    'source' => $token,
    'receipt_email' => $user->email,]
);
```

如果支付失败`charge`方法将返回`false`，这通常表明付款被拒绝：

```php
if ( ! $user->charge(100)) {
    // The charge was denied...
}
```

如果支付成功，该方法将会返回一个完整的 Stripe 响应。

--------------------------------------------------------------------------------

## 9.4.5 发票

你可以使用`invoices`方法轻松获取账单模型的发票数组：

```php
$invoices = $user->invoices();
```

当列出客户发票时，你可以使用发票的辅助函数来显示相关的发票信息。

例如，你可能想要在表格中列出每张发票，从而方便用户下载它们：

```php
<table>
    @foreach ($invoices as $invoice)
        <tr>
            <td>{{ $invoice->dateString() }}</td>
            <td>{{ $invoice->dollars() }}</td>
            <td><a href="/user/invoice/{{ $invoice->id }}">Download</a></td>
        </tr>
    @endforeach
</table>
```

### 9.4.5.1 生成PDF发票

在生成PDF分票之前，需要安装 PHP 库`dompdf`：

```php
composer require dompdf/dompdf
```

在路由或控制器中，使用`downloadInvoice`方法生成发票的 PDF 下载，该方法将会自动生成相应的 HTTP 响应发送下载到浏览器：

```php
Route::get('user/invoice/{invoice}', function ($invoiceId) {
    return Auth::user()->downloadInvoice($invoiceId, [
        'vendor'  => 'Your Company',
        'product' => 'Your Product',
    ]);
});
```

--------------------------------------------------------------------------------
