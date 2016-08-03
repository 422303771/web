<!-- toc -->

- [9.23 测试](#923-测试)

  - [9.23.1 简介](#9231-简介)

    - [9.23.1.1 测试环境](#92311-测试环境)
    - [9.23.1.2 定义&运行测试](#92312-定义运行测试)

  - [9.23.2 应用测试](#9232-应用测试)

    - [9.23.2.1 与应用交互](#92321-与应用交互)
    - [9.23.2.2 测试JSON API](#92322-测试json-api)
    - [9.23.2.3 Session/认证](#92323-session认证)
    - [9.23.2.4 禁止中间件](#92324-禁止中间件)
    - [9.23.2.5 自定义HTTP请求](#92325-自定义http请求)
    - [9.23.2.6 PHPUnit断言方法](#92326-phpunit断言方法)

  - [9.23.3 处理数据库](#9233-处理数据库)

    - [9.23.3.1 每次测试后重置数据库](#92331-每次测试后重置数据库)
    - [9.23.3.2 模型工厂](#92332-模型工厂)

  - [9.23.4 模拟](#9234-模拟)

    - [9.23.4.1 模拟事件](#92341-模拟事件)
    - [9.23.4.2 模拟队列任务](#92342-模拟队列任务)
    - [9.23.4.3 模拟门面](#92343-模拟门面)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# 9.23 测试

## 9.23.1 简介

Laravel 植根于测试，实际上，内置使用`PHPUnit`对测试提供支持是即开即用的，并且`phpunit.xml`文件已经为应用设置好了。

框架还提供了方便的辅助方法允许你对应用进行富有表现力的测试。

`tests`目录中提供了一个 `ExampleTest.php` 文件，安装完新的 Laravel 应用后，只需简单在命令行运行`phpunit`来运行测试。

### 9.23.1.1 测试环境

运行测试的时候，Laravel 自动设置配置环境为`testing`。Laravel在测试时自动配置`session`和`cache`驱动为数组驱动，这意味着测试时不会持久化存储`session`和`cache`。

如果需要的话你也可以创建其它测试环境配置。`testing`环境变量可以在`phpunit.xml`文件中配置。

### 9.23.1.2 定义&运行测试

要创建一个新的测试用例，可以使用如下Artisan命令：

```
php artisan make:test UserTest
```

该命令将会在`tests`目录下生成一个新的`UserTest`类。然后你可以使用`PHPUnit`定义测试方法。要运行测试，简单从终端执行`phpunit`命令即可：

```php
<?php

use Illuminate\Foundation\Testing\WithoutMiddleware;
use Illuminate\Foundation\Testing\DatabaseMigrations;
use Illuminate\Foundation\Testing\DatabaseTransactions;

class UserTest extends TestCase{
    /**
     * A basic test example.
     *
     * @return void
     */
    public function testExample()
    {
        $this->assertTrue(true);
    }
}
```

_注意：如果你在测试类中定义自己的`setUp`方法，确保在其中调用`parent::setUp`。_

--------------------------------------------------------------------------------

## 9.23.2 应用测试

Laravel 为生成`HTTP`请求、测试输出、以及填充表单提供了平滑的API。

举个例子，我们看下`tests`目录下包含的`ExampleTest.php`文件：

```php
<?php

use Illuminate\Foundation\Testing\WithoutMiddleware;
use Illuminate\Foundation\Testing\DatabaseTransactions;

class ExampleTest extends TestCase{
    /**
     * 基本功能测试示例
     *
     * @return void
     */
    public function testBasicExample()
    {
        $this->visit('/')
             ->see('Laravel 5')
             ->dontSee('Rails');
    }
}
```

`visit`方法生成了一个`GET`请求，`see`方法对我们从应用返回响应中应该看到的给定文本进行断言。`dontSee`方法对给定文本没有从应用响应中返回进行断言。在Laravel中这是最基本的有效应用测试。

### 9.23.2.1 与应用交互

当然，除了对响应文本进行断言之外还有做更多测试，让我们看一些点击链接和填充表单的例子：

- **点击链接**

  在本测试中，我们将为应用生成请求，在返回的响应中`点击`链接，然后对访问`URI`进行断言。

  例如，假定响应中有一个`关于我们`的链接：

  ```
    <a href="/about-us">About Us</a>
  ```

  现在，让我们编写一个测试点击链接并断言用户访问页面是否正确：

  ```php
    public function testBasicExample(){
        $this->visit('/')
             ->click('About Us')
             ->seePageIs('/about-us');
    }
  ```

- **处理表单**

  Laravel 还为处理表单提供了多个方法。`type`, `select`, `check`, `attac`h, 和`press`方法允许你与所有表单输入进行交互。

  例如，我们假设这个表单存在于应用注册页面：

  ```html
    <form action="/register" method="POST">
        {!! csrf_field() !!}

        <div>
            Name: <input type="text" name="name">
        </div>

        <div>
            <input type="checkbox" value="yes" name="terms"> Accept Terms
        </div>

        <div>
            <input type="submit" value="Register">
        </div>
    </form>
  ```

  我们可以编写测试完成表单并检查结果：

  ```php
    public function testNewUserRegistration(){
        $this->visit('/register')
             ->type('Taylor', 'name')
             ->check('terms')
             ->press('Register')
             ->seePageIs('/dashboard');
    }
  ```

  当然，如果你的表单包含其他输入比如单选按钮或下拉列表，也可以轻松填写这些字段类型。这里是所有表单操作方法列表：

方法                                       | 描述
:--------------------------------------- | :-------------------
$this->type($text, $elementName)         | "Type" 文本到给定字段
$this->select($value, $elementName)      | "Select" 单选框或下拉列表
$this->check($elementName)               | "Check" 复选框
$this->attach($pathToFile, $elementName) | "Attach" 文件到表单
$this->press($buttonTextOrElementName)   | "Press" 给定文本或name的按钮
$this->uncheck($elementName)             | "Uncheck"复选框

- **处理附件**

### 9.23.2.2 测试JSON API

- **验证JSON值匹配**

- **验证JSON数据结构匹配**

### 9.23.2.3 Session/认证

### 9.23.2.4 禁止中间件

### 9.23.2.5 自定义HTTP请求

### 9.23.2.6 PHPUnit断言方法

--------------------------------------------------------------------------------

## 9.23.3 处理数据库

### 9.23.3.1 每次测试后重置数据库

- **使用迁移**

- **使用事务**

### 9.23.3.2 模型工厂

- **多个工厂类型**

- **在测试中使用工厂**

- **持久化工厂模型**

- **添加关联关系到模型**

--------------------------------------------------------------------------------

## 9.23.4 模拟

### 9.23.4.1 模拟事件

### 9.23.4.2 模拟队列任务

### 9.23.4.3 模拟门面

--------------------------------------------------------------------------------
