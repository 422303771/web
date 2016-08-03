# 5.4 请求

## 5.4.1 获取请求

通过依赖注入获取当前HTTP请求实例，应该在控制器的构造函数或方法中对`Illuminate\Http\Request`类进行类型提示，当前请求实例会被服务容器自动注入：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

class UserController extends Controller
{
    /**
     * 存储新用户
     *
     * @param Request $request
     * @return Response
     */
    public function store(Request $request)
    {
        $name=$request->input('name');

        //
    }
}
```

如果你的控制器方法还期望获取路由参数输入，只需要将路由参数置于其它依赖之后即可，例如，如果你的路由定义如下：

```
Route::put('user/{id}','UserController@update');
```

依然可以对`Illuminate\Http\Request`进行类型提示并通过如下方式定义控制器方法来访问路由参数：

```php
<?php

namespace App\Http\Controllers;

use Illuminate\Http\Request;
use Illuminate\Routing\Controller;

classUser Controller extends Controller
{
    /**
     * 更新指定用户
     *
     * @param Request $request
     * @param int $id
     * @return Response
     */
    public function update(Request $request,$id)
    {
        //
    }
}
```

### 5.4.1.1 基本请求信息

`Illuminate\Http\Request`实例提交了多个方法来检查应用的HTTP请求，laravel的`Illuminate\Http\Request`继承自`Symfony\Component\HttpFoundation\Request`类，下面演示了一些有用的方法：

- **获取请求URI**

  path方法将会返回请求的URI（返回的是域名之后的部分），如果进入的请求路径是`http://domain.com/foo/bar`,则`path`方法将会返回`foo/bar`:

  ```
    $uri=$request->path();
  ```

  `is`方法允许你验证进入的请求是否与给定模式匹配。使用该方法可以使用`*`通用符：

  ```php
    if($request->is('admin/*')){
    //
    }
  ```

  想要获取完整的URL，而不仅仅是路径信息，可以使用请求实例中的`url`或`fullUrl`方法：

  ```php
    $url=$request->url();
    $url = $request->fullUrl();
  ```

- **获取请求方法**

  `method`方法将会返回请求的HTTP请求方式。你可以使用`isMethod`方法来验证HTTP请求方式是否匹配给定字符串：

  ```php
    $method=$request->method();
    if($request->isMethod('post')){
        //
    }
  ```

### 5.4.1.2 PSR-7 请求

PSR-7标准指定了HTTP信息接口，包括请求和响应。如果想要获取PSR-7请求实例，首先需要安装一些库，laravel使用`Symfony HTTP Message Bridge`组件，将原的laravel请求和响应转化为PSR-7兼容的请求：

```
composer require symfony/psr-http-message-bridge
composer require zendframework/zend-diactoros
```

安装完这些库之后，只需要在路由或控制器中通过，对请求类型进行类型提示就可以获取RSR-7请求：

```php
use Psr\Http\Message\ServerRequestInterface;

Route::get('/', function (ServerRequestInterface $request) {
//
});
```

如果从路由或控制器返回的是PSR-7响应实例，则其将会自动转化为laravel响应实例并显示出来。

--------------------------------------------------------------------------------

## 5.4.2 获取输入信息

- **获取特定输入值**

  通过`Illuminate\Http\Request`实例，使用简单的方法就可以取得所有的使用者输入资料。

  不需要担心发出请求时使用的HTTP动词，获取输入资料的方式都是相同的。

  ```
    $name = $request->input('name');
  ```

  此外，可以使用`Illuminate\Http\Request`的动态属性存取使用者输入。

  例如，如果你的应用程序的表单含有一个`name`字段，可以使用下面的方式提交：

  ```
    $name = $request->name;
  ```

  还可以传统一个默认值作为第二个参数给`input`方法，如果请求输入值在当前请求未出现时该值将会返回：

  ```
    $name = $request->input('name', 'Sally');
  ```

  处理表单数组输入时，可以使用`.`来访问数组输入：

  ```
    $input = $request->input('products.0.name');
    $names = $request->input('products.*.name');
  ```

- **判断输入值是否出现**

  判断值是否在请求中出现，可以使用`has`方法，如果值出现且不为空，`has`方法返回`true`:

  ```
    if ($request->has('name')) {
        //
    }
  ```

- **获取所有输入数据**

  还可以通过`all`方法获取所有输入数据：

  ```
    $input = $request->all();
  ```

- **获取部分输入数据**

  如何需要取出输入数据的子集，可以使用`only`或`except`方法，这两个方法都接收一个数组或动态列表作为唯一参数:

  ```
    $input = $request->only(['username', 'password']);
    $input = $request->only('username', 'password');

    $input = $request->except(['credit_card']);
    $input = $request->except('credit_card');
  ```

### 5.4.2.1 上一次请求输入

laravel 允许你在两次请求之间保存输入数据，这个特性在检测校验数据失败后需要重新填充表单数据时很有用。

但如果使用的是laravel内置的验证服务，则不需要手动使用这些方法，因为一些laravel内置的校验会自动调用它们。

- **将输入存储到一次性Session**

  `Illuminate\Http\Request`实例的`flash`方法会将当前输入存放到一次性`Session`中，下次使用者发出请求至应用程序时就可以使用它们：

  ```
    $request->flash();
  ```

  还可以使用`flashOnly`和`flashExcept`方法将输入数据子集存储到Session中：

  ```
    $request->flashOnly('username', 'email');
    $request->flashExcept('password');
  ```

- **将输入存储到一次性Session然后重定向**

  可能常常需要使用Session中的数据，并重新定向到前一页。可以简单使用`withInput`方法来将输入数据链接到`redirect`后面:

  ```
    return redirect('form')->withInput();
    return redirect('form')->withInput($request->except('password'));
  ```

- **获取上次输入数据**

  要从Session中取出上次请求的输入数据，可以使用Request实例的`old`方法。`old`方法提供了便利方式从Session中取出数据：

  ```
    $username = $request->old('username');
  ```

  laravel还提供了一个全局的辅助函数`old`，如果在Blade模板中显示上次输入数据，使用辅助函数`old`更方便，如果给定参数没有对应输入，返回null：

  ```
    {{ old('username') }}
  ```

### 5.4.2.2 Cookies

- **从请求中取出Cookies**

  laravel 框架创建的所有cookies都经过加密并使用一个认证码进行签名，这意味着如果客户端修改了它们则需要对其进行有效性验证。使用`Illuminate\Http\Request`类中的 `cookie`方法从请求中获取cookie的值：

  ```
    $value = $request->cookie('name');
  ```

- **新增Cookies到回应**

  laravel 提供一个全局辅助函数`cookie`作为一个简单工厂来生成新的`Symfony\Component\HttpFoundation\Cookie`实例，新增的`cookie`通过`withCookie`方法被附加到`Illuminate\Http\Response`实例：

  ```
    $response = new Illuminate\Http\Response('Hello World');
    $response->withCookie(cookie('name', 'value', $minutes));
    return $response;
  ```

  想要创建一个长期有效的cookie，可以使用cookie工厂的`forever`方法：

  ```
    $response->withCookie(cookie()->forever('name', 'value'));
  ```

### 5.4.2.3 文件上传

- **获取上传的文件**

  使用`Illuminate\Http\Request`实例的`file`方法来访问上传文件，该方法返回的对象是`Symfony\Component\HttpFoundation\File\UploadedFile`类的一个实例，该类继承自PHP标准库中，与文件交互的`SplFileInfo`类：

  ```
    $file = $request->file('photo');
  ```

  可以使用`hasFile`方法，判断上传文件是否存在：

  ```
    if ($request->hasFile('photo')) {
        //
    }
  ```

- **验证文件是否上传成功**

  使用`isValid`方法判断文件在上传过程中是否出错：

  ```
    if ($request->file('photo')->isValid()){
        //
    }
  ```

- **保存上传的文件**

  如果要移动文件到新位置，必须使用`move`方法。该方法会将文件从暂存位置（根据机器PHP设定来确定）移动到指定的永久保存位置：

  ```php
    $request->file('photo')->move($destinationPath);

    $request->file('photo')->move($destinationPath, $fileName);
  ```

- **其它文件方法**

  `UploadedFile`实例中有很多可用的方法。可以到[该类的API](http://api.symfony.com/2.7/Symfony/Component/HttpFoundation/File/UploadedFile.html)了解。
