<!--toc-->

- [HTML 表单](#html-表单)
	- [HTML 基本表单](#html-基本表单)
		- [`<input>`输入标签](#input输入标签)
			- [`text`使用方法](#text使用方法)
			- [`radio`使用方法](#radio使用方法)
			- [`submit`使用方法](#submit使用方法)
			- [`name`属性](#name属性)
			- [`<fieldset>`标签](#fieldset标签)
			- [`<form>`标签的其他设置](#form标签的其他设置)
		- [`action`属性](#action属性)
			- [`GET`](#get)
			- [`POST`](#post)
	- [HTML 表单元素](#html-表单元素)
		- [`<select>`下拉式选项](#select下拉式选项)
		- [`<textarea>`大型文本输入](#textarea大型文本输入)
		- [`<button>`按钮](#button按钮)
		- [`<datalist>`带备选项的输入框](#datalist带备选项的输入框)
		- [`<keygen>`对用户验证](#keygen对用户验证)
		- [`<output>`限定输出结果](#output限定输出结果)
	- [HTML 输入类型](#html-输入类型)
		- [`input`的`password`属性](#input的password属性)
		- [`input`的`checkbox`属性](#input的checkbox属性)
		- [`input`的`number`属性](#input的number属性)
		- [`input`的`date`属性](#input的date属性)
		- [`input`的`color`属性](#input的color属性)
		- [`input`的`range`属性](#input的range属性)
		- [`input`的`month`属性](#input的month属性)
		- [`input`的`week`属性](#input的week属性)
		- [`input`的`time`属性](#input的time属性)
		- [`input`的`datetime-local`属性](#input的datetime-local属性)
		- [`input`的`email`属性](#input的email属性)
		- [`input`的`search`属性](#input的search属性)
	- [HTML 属性输入](#html-属性输入)
		- [`input`中一些属性值得限制](#input中一些属性值得限制)
		- [`value`](#value)
		- [`readonly`](#readonly)
		- [`disabled`](#disabled)
		- [`size`](#size)
		- [`maxlength`](#maxlength)
		- [`autocomplete`](#autocomplete)
		- [`autofocus`](#autofocus)
		- [`formaction`](#formaction)
		- [`formenctype`](#formenctype)
		- [`formmethod`](#formmethod)
		- [`formtarget`](#formtarget)
		- [`height&width`](#heightwidth)
		- [`multiple`](#multiple)
		- [`placeholder`](#placeholder)
		- [`step`](#step)

<!-- tocstop -->

----

# HTML 表单

----

## HTML 基本表单

表单使用`<form>`标签，主要用来提交数据，与输入信息。

### `<input>`输入标签

`<input>`标签使用`type`属性设置，输入方式。常用输入方式如下：

- `text`文本框
- `radio`单选框，一个`<form>`中只能有一个有效选择。
- `submit`提交按钮，用来提交表单。

#### `text`使用方法

```HTML
<form>
  First name:<br>
  <input type="text" name="firstname"><br>
  Last name:<br>
  <input type="text" name="lastname">
</form>
```
![sp160808_132934](http://ooo.0o0.ooo/2016/08/08/57a818eb7e78b.png)

<!-- ![sp160808_132934](/assets/sp160808_132934.png) -->

#### `radio`使用方法

```HTML
<form>
  <input type="radio" name="gender" value="male" checked> Male<br>
  <input type="radio" name="gender" value="female"> Female<br>
  <input type="radio" name="gender" value="other"> Other
</form>
```
![sp160808_133047](http://ooo.0o0.ooo/2016/08/08/57a8193394712.png)

<!-- ![sp160808_133047](/assets/sp160808_133047.png) -->

#### `submit`使用方法

```HTML
<form action="action_page.php">
  First name:<br>
  <input type="text" name="firstname" value="Mickey"><br>
  Last name:<br>
  <input type="text" name="lastname" value="Mouse"><br><br>
  <input type="submit" value="Submit">
</form>
```
![sp160808_133324](http://ooo.0o0.ooo/2016/08/08/57a819ceec684.png)

<!-- ![sp160808_133324](/assets/sp160808_133324.png) -->

#### `name`属性

想要正确的提交数据，每个`<input>`中必须有一个`name`属性。

#### `<fieldset>`标签

`<fieldset>`标签长于`<legend>`一起使用。

- `<fieldset>`为表单画上边框。
- `<legend>`为表单加上标题。

```HTML
<form action="action_page.php">
  <fieldset>
    <legend>Personal information:</legend>
    First name:<br>
    <input type="text" name="firstname" value="Mickey">
    <br>
    Last name:<br>
    <input type="text" name="lastname" value="Mouse">
    <br><br>
    <input type="submit" value="Submit">
  </fieldset>
</form>
```
![sp160808_140313](http://ooo.0o0.ooo/2016/08/08/57a820caa457e.png)

<!-- ![sp160808_140313](/assets/sp160808_140313.png) -->

#### `<form>`标签的其他设置

- `accept-charset`设置提交字符集。(默认为页面字符集)
- `action`设置提交地址。(默认为当前递交页)
- `autocomplete`设置自动填充。(默认为开启)
- `enctype`指定提交数据的编码方式，(默认是URL编码)
- `method`表单提交方法。(默认为：GET)
- `name`指定识别名称
- `novalidate`指定浏览器不验证表单。
- `target`指定表单提交后的打开方式(默认为`_self`)

----

### `action`属性

`action`属性是提交表单时常用的，说到`action`就一定会说到`get`与`post`方式。

```HTML
<form action="action_page.php" method="get">
<form action="action_page.php" method="post">
```
#### `GET`

使用`GET`时，表单的内容是可见的。

#### `POST`

使用`POST`时，表单的内容是不可见的，相对更加安全。

如果使用了信息通信加密，`GET`与`POST`的区别就不大了。


----

## HTML 表单元素

### `<select>`下拉式选项

在`option`中设置`selected`,将选项设置成默认。如果不设置，默认为第一个`option`。

```HTML
<select name="cars">
  <option value="volvo">Volvo</option>
  <option value="saab">Saab</option>
  <option value="fiat">Fiat</option>
  <option value="audi">Audi</option>
</select>
```
![sp160808_143845](http://ooo.0o0.ooo/2016/08/08/57a8292b600ce.png)

<!-- ![sp160808_143845](/assets/sp160808_143845.png) -->

### `<textarea>`大型文本输入

```HTML
<textarea name="message" rows="10" cols="30">
The cat was playing in the garden.
</textarea>
```
![sp160808_144302](http://ooo.0o0.ooo/2016/08/08/57a82a2284119.png)

<!-- ![sp160808_144302](/assets/sp160808_144302.png) -->

### `<button>`按钮

```HTML
<button type="button" onclick="alert('Hello World!')">Click Me!</button>
```
![sp160808_144350](http://ooo.0o0.ooo/2016/08/08/57a82a522868d.png)

<!-- ![sp160808_144350](/assets/sp160808_144350.png) -->


### `<datalist>`带备选项的输入框

```HTML
<form action="action_page.php">
  <input list="browsers">
  <datalist id="browsers">
    <option value="Internet Explorer">
    <option value="Firefox">
    <option value="Chrome">
    <option value="Opera">
    <option value="Safari">
  </datalist>
</form>
```
![sp160808_144427](http://ooo.0o0.ooo/2016/08/08/57a82a81d323b.png)

<!-- ![sp160808_144427](/assets/sp160808_144427.png) -->

### `<keygen>`对用户验证

```HTML
<form action="action_page.php">
  Username: <input type="text" name="user">
  Encryption: <keygen name="security">
  <input type="submit">
</form>
```
![sp160808_144602](http://ooo.0o0.ooo/2016/08/08/57a82ad805676.png)

<!-- ![sp160808_144602](/assets/sp160808_144602.png) -->

### `<output>`限定输出结果

```HTML
<form action="action_page.php"
  oninput="x.value=parseInt(a.value)+parseInt(b.value)">
  0
  <input type="range"  id="a" name="a" value="50">
  100 +
  <input type="number" id="b" name="b" value="50">
  =
  <output name="x" for="a b"></output>
  <br><br>
  <input type="submit">
</form>
```
![sp160808_144655](http://ooo.0o0.ooo/2016/08/08/57a82b08d6436.png)

<!-- ![sp160808_144655](/assets/sp160808_144655.png) -->

_注意:`<datalist>`、`<keygen>`、`<output>`为HTML5特有。_

----

## HTML 输入类型

### `input`的`password`属性

```HTML
<form>
  User name:<br>
  <input type="text" name="username"><br>
  User password:<br>
  <input type="password" name="psw">
</form>
```
![sp160808_145628](http://ooo.0o0.ooo/2016/08/08/57a82d51957ca.png)

<!-- ![sp160808_145628](/assets/sp160808_145628.png) -->

### `input`的`checkbox`属性

```HTML
<form>
  <input type="checkbox" name="vehicle1" value="Bike"> I have a bike<br>
  <input type="checkbox" name="vehicle2" value="Car"> I have a car
</form>
```
![sp160808_145750](http://ooo.0o0.ooo/2016/08/08/57a82d9a70930.png)

<!-- ![sp160808_145750](/assets/sp160808_145750.png) -->

__下方为HTML5增加属性__


### `input`的`number`属性

```HTML
<form>
  Quantity (between 1 and 5):
  <input type="number" name="quantity" min="1" max="5">
</form>
```
![sp160808_150020](http://ooo.0o0.ooo/2016/08/08/57a82e2e5a3f3.png)

<!-- ![sp160808_150020](/assets/sp160808_150020.png) -->

### `input`的`date`属性

不支持`firefox`

```HTML
<form>
  Enter a date before 1980-01-01:
  <input type="date" name="bday" max="1979-12-31"><br>
  Enter a date after 2000-01-01:
  <input type="date" name="bday" min="2000-01-02"><br>
</form>
```

![sp160808_151908](http://ooo.0o0.ooo/2016/08/08/57a832bb662d8.png)

<!-- ![sp160808_151908](/assets/sp160808_151908.png) -->

### `input`的`color`属性

不支持`IE`、`firefox`

```HTML
<form>
  Select your favorite color:
  <input type="color" name="favcolor">
</form>
```
![sp160808_152041](http://ooo.0o0.ooo/2016/08/08/57a832f576e2d.png)

<!-- ![sp160808_152041](/assets/sp160808_152041.png) -->

### `input`的`range`属性

```HTML
<form>
  <input type="range" name="points" min="0" max="10">
</form>
```
![sp160808_152134](http://ooo.0o0.ooo/2016/08/08/57a83327346ed.png)

<!-- ![sp160808_152134](/assets/sp160808_152134.png) -->

### `input`的`month`属性

不支持`firefox`

```HTML
<form>
  Birthday (month and year):
  <input type="month" name="bdaymonth">
</form>
```
![sp160808_152238](http://ooo.0o0.ooo/2016/08/08/57a833672d86c.png)

<!-- ![sp160808_152238](/assets/sp160808_152238.png) -->

### `input`的`week`属性

不支持`firefox`

```HTML
<form>
  Select a week:
  <input type="week" name="week_year">
</form>
```
![sp160808_152339](http://ooo.0o0.ooo/2016/08/08/57a833ad32220.png)

<!-- ![sp160808_152339](/assets/sp160808_152339.png) -->

### `input`的`time`属性

不支持`firefox`

```HTML
<form>
  Select a time:
  <input type="time" name="usr_time">
</form>
```
![sp160808_152436](http://ooo.0o0.ooo/2016/08/08/57a833f79c2f3.png)

<!-- ![sp160808_152436](/assets/sp160808_152436.png) -->

### `input`的`datetime-local`属性

不支持`firefox`

```HTML
<form>
  Birthday (date and time):
  <input type="datetime-local" name="bdaytime">
</form>
```
![sp160808_152646](http://ooo.0o0.ooo/2016/08/08/57a8346610421.png)

<!-- ![sp160808_152646](/assets/sp160808_152646.png) -->

### `input`的`email`属性

不支持`safari`

```HTML
<form>
  E-mail:
  <input type="email" name="email">
</form>
```

![sp160808_152734](http://ooo.0o0.ooo/2016/08/08/57a834a5688e2.png)

<!-- ![sp160808_152734](/assets/sp160808_152734.png) -->

### `input`的`search`属性

`search`就是一个普通的`text`输入框。

```HTML
<form>
  Search Google:
  <input type="search" name="googlesearch">
</form>
```

_考虑到兼容性，只是有最基本的就好。_

----

## HTML 属性输入

### `input`中一些属性值得限制

- `disabled`禁止指定字段
- `max`最大值范围
- `maxlength`指定最大数值，仅数字
- `min`最小值范围
- `pattern`检查输入值
- `readonly`设定为不能更改
- `required`指定为必填
- `size`尺寸
- `step`一次步进值
- `value`默认值

### `value`

```HTML
<form action="">
First name:<br>
<input type="text" name="firstname" value="John">
<br>
Last name:<br>
<input type="text" name="lastname">
</form>
```
普通输入框

![sp160808_171420](http://ooo.0o0.ooo/2016/08/08/57a84d9a5ad84.png)

<!-- ![sp160808_171420](/assets/sp160808_171420.png) -->

### `readonly`

```HTML
<form action="">
First name:<br>
<input type="text" name="firstname" value ="John" readonly>
<br>
Last name:<br>
<input type="text" name="lastname">
</form>
```
`readonly`为不能输入状态。只显示预设信息。

### `disabled`

```HTML
<form action="">
First name:<br>
<input type="text" name="firstname" value="John" disabled>
<br>
Last name:<br>
<input type="text" name="lastname">
</form>
```
![sp160808_171917](http://ooo.0o0.ooo/2016/08/08/57a84ec02e98a.png)

<!-- ![sp160808_171917](/assets/sp160808_171917.png) -->

### `size`

```HTML
<form action="">
First name:<br>
<input type="text" name="firstname" value ="John" size="40">
<br>
Last name:<br>
<input type="text" name="lastname">
</form>
```
![sp160808_172004](http://ooo.0o0.ooo/2016/08/08/57a84eee274a2.png)

<!-- ![sp160808_172004](/assets/sp160808_172004.png) -->

### `maxlength`

```HTML
<form action="">
First name:<br>
<input type="text" name="firstname" maxlength="10">
<br>
Last name:<br>
<input type="text" name="lastname">
</form>
```
限制字符长度。一般结合JS使用。

__下方是HTML5增加,只记录全浏览器支持。__

### `autocomplete`
```HTML
<form action="action_page.php" autocomplete="on">
  First name:<input type="text" name="fname"><br>
  Last name: <input type="text" name="lname"><br>
  E-mail: <input type="email" name="email" autocomplete="off"><br>
  <input type="submit">
</form>
```
当`autocomplete`设置为`on`，在以及填写过的信息中选择。`off`时则关闭。

### `autofocus`

```HTML
<form action="action_page.php">
  First name:<input type="text" name="fname" autofocus><br>
  Last name: <input type="text" name="lname"><br>
  <input type="submit">
</form>
```
`autofocus`被设置为自动填写状态。

### `formaction`

```HTML
<form action="action_page.php">
  First name: <input type="text" name="fname"><br>
  Last name: <input type="text" name="lname"><br>
  <input type="submit" value="Submit"><br>
  <input type="submit" formaction="demo_admin.asp"
  value="Submit as admin">
</form>
```
`formaction`覆盖`<form>的action属性`。

### `formenctype`

```HTML
<form action="demo_post_enctype.asp" method="post">
  First name: <input type="text" name="fname"><br>
  <input type="submit" value="Submit">
  <input type="submit" formenctype="multipart/form-data"
  value="Submit as Multipart/form-data">
</form>
```
`formenctype`覆盖`<form>的enctype属性`。

### `formmethod`

```HTML
<form action="action_page.php" method="get">
  First name: <input type="text" name="fname"><br>
  Last name: <input type="text" name="lname"><br>
  <input type="submit" value="Submit">
  <input type="submit" formmethod="post" formaction="demo_post.asp"
  value="Submit using POST">
</form>
```
`formmethod`覆盖`<form>的method属性`。

### `formtarget`

```HTML
<form action="action_page.php">
  First name: <input type="text" name="fname"><br>
  Last name: <input type="text" name="lname"><br>
  <input type="submit" value="Submit as normal">
  <input type="submit" formtarget="_blank"
  value="Submit to a new window">
</form>
```
`formtarget`覆盖`<form>的target属性`。

### `height&width`

`height`与`width`只能使用在`<input type="image">`当中。

```HTML
<input type="image" src="img_submit.gif" alt="Submit" width="48" height="48">
```

### `multiple`
```HTML
<form action="action_page.php">
  Select images: <input type="file" name="img" multiple>
  <input type="submit">
</form>
```
允许用户输出多个值，只在`email`与`file`中有效。

### `placeholder`

```HTML
<form action="action_page.php">
  <input type="text" name="fname" placeholder="First name"><br>
  <input type="text" name="lname" placeholder="Last name"><br>
  <input type="submit" value="Submit">
</form>
```
![sp160808_174036](http://ooo.0o0.ooo/2016/08/08/57a853bf5a8b6.png)

<!-- ![sp160808_174036](/assets/sp160808_174036.png) -->

可以使用在`text`、`search`、`url`、`tel`、`email`、`password`中。

### `step`

```HTML
<input type="number" name="points" step="3">
```
设置步进，可以使用在`number`、`range`、`date`、`datetime-local`、`month`、`time`、`week`。

----
