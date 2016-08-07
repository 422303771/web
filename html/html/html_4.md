<!-- toc -->

- [HTML Link、图片与表格](#html-link-图片与表格)
	- [:link:Link](#linklink)
		- [link 基本语法](#link-基本语法)
		- [link打开方法](#link打开方法)
		- [图片link](#图片link)
		- [书签](#书签)
	- [图片](#图片)
	- [表格](#表格)
	- [List](#list)
		- [`<ul>`标签](#ul标签)
		- [`<ol>`标签](#ol标签)
		- [`<dl>`标签](#dl标签)
		- [`<li>`标签](#li标签)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# HTML Link、图片与表格

## :link:Link

### link 基本语法

```html
  <a href="url">link text</a>
```

- 本地连接

  ```html
  <a href="html_images.asp">HTML Images</a>
  ```

- 连接行为

  ```html
  <style>
  a:link {
    color: green;
    background-color: transparent;
    text-decoration: none;
  }
  a:visited {
    color: pink;
    background-color: transparent;
    text-decoration: none;
  }
  a:hover {
    color: red;
    background-color: transparent;
    text-decoration: underline;
  }
  a:active {
    color: yellow;
    background-color: transparent;
    text-decoration: underline;
  }
  </style>
  ```

语法        | 效果
:-------- | :-----------
a:link    | 连接本来样式
a:visited | 连接查看后样式
a:hover   | 鼠标移动到时，连接的样式
a:active  | 鼠标点击时，连接的样式

### link打开方法

```HTML
<a href="http://www.w3schools.com/html/" target="_top">HTML5 tutorial!</a>
```

语法        | 效果
:-------- | :-----------
\_blank  | 在新窗口或标签打开
\_self | 在被点击的窗口中打开，页面不发送跳转。(默认情况)
\_parent  | 在父窗口中跳转
\_top | 在一个全新的窗口中打开，类似弹出窗口。

### 图片link

```HTML
<a href="default.asp">
  <img src="smiley.gif" alt="HTML tutorial" style="width:42px;height:42px;border:0;">
</a>
```
### 书签

```HTML
<a href="#tips">Visit the Useful Tips Section</a>
```

----

## 图片

基本用法
```
<img src="pic_mountain.jpg" alt="Mountain View" style="width:304px;height:228px;">
```
这里设置长与高，是为了预留出图片位置。`alt`为图片不能加载时，显示的文字。

图片与字体混合

在`style`中添加`float:right`或者`float:left`来设定图片位置。

```HTML
<img src="planets.gif" alt="Planets" usemap="#planetmap" style="width:145px;height:126px;">

<map name="planetmap">
  <area shape="rect" coords="0,0,82,126" alt="Sun" href="sun.htm">
  <area shape="circle" coords="90,58,3" alt="Mercury" href="mercur.htm">
  <area shape="circle" coords="124,58,8" alt="Venus" href="venus.htm">
</map>
```
上方代码设定在图片中的载入区域，但是并不常用。

----

## 表格

下方为常见表格代码：
```HTML
<!DOCTYPE html>
<html>
<head>
<style>
table {
    font-family: arial, sans-serif;
    border-collapse: collapse;
    width: 100%;
}

td, th {
    border: 1px solid #dddddd;
    text-align: left;
    padding: 8px;
}

tr:nth-child(even) {
    background-color: #dddddd;
}
</style>
</head>
<body>

<table>
  <tr>
    <th>Company</th>
    <th>Contact</th>
    <th>Country</th>
  </tr>
  <tr>
    <td>Alfreds Futterkiste</td>
    <td>Maria Anders</td>
    <td>Germany</td>
  </tr>
  <tr>
    <td>Centro comercial Moctezuma</td>
    <td>Francisco Chang</td>
    <td>Mexico</td>
  </tr>
  <tr>
    <td>Ernst Handel</td>
    <td>Roland Mendel</td>
    <td>Austria</td>
  </tr>
  <tr>
    <td>Island Trading</td>
    <td>Helen Bennett</td>
    <td>UK</td>
  </tr>
  <tr>
    <td>Laughing Bacchus Winecellars</td>
    <td>Yoshi Tannamuri</td>
    <td>Canada</td>
  </tr>
  <tr>
    <td>Magazzini Alimentari Riuniti</td>
    <td>Giovanni Rovelli</td>
    <td>Italy</td>
  </tr>
</table>

</body>
</html>
```
![sp160807_162042](http://ooo.0o0.ooo/2016/08/07/57a6ef86d7cd0.png)

<!-- ![sp160807_162042](/assets/sp160807_162042.png) -->

`style`中的`width`指页面宽度。

- `<table>` 用来定义表格
- `<tr>` 表格的行
- `<td>` 表格行的元素
- `<th>` 表格的行标题
- `<caption>` 表格的标题
- `border` 表格边线
- `border-collapse` 边线样式。`collapse`为无边距。
- `padding` 表格宽度
- `text-align` 字体位置,`left`,`right`
- `border-spacing` 表格元素与元素间距
- `colspan` 合并表格行。可以用在`<tr>`中使用。
- `rowspan` 合并表格列。可以用在`<th>`中使用。
- `id`指定表格样式，在`<table>`标签中使用。

`colspan`用法
```HTML
<tr>
	<th>Name</th>
	<th colspan="2">Telephone</th>
</tr>
```
效果：

![sp160807_162923](http://ooo.0o0.ooo/2016/08/07/57a6f18a32d9e.png)

<!-- ![sp160807_162923](/assets/sp160807_162923.png) -->


`rowspan`用法
```HTML
<tr>
	<th rowspan="2">Telephone:</th>
	<td>55577854</td>
</tr>
```
效果：

![sp160807_162950](http://ooo.0o0.ooo/2016/08/07/57a6f1a5d910f.png)

<!-- ![sp160807_162950](/assets/sp160807_162950.png) -->

---

## List

### `<ul>`标签

```HTML
<!DOCTYPE html>
<html>
<body>

<h2>Unordered List with Disc Bullets</h2>

<ul style="list-style-type:disc">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ul>

<ul style="list-style-type:circle">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ul>

<ul style="list-style-type:square">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ul>

<ul style="list-style-type:none">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ul>

</body>
</html>
```
效果

![sp160807_164054](http://ooo.0o0.ooo/2016/08/07/57a6f440a7c90.png)

<!-- ![sp160807_164054](/assets/sp160807_164054.png) -->

----

`<ul>`标签使用`style`中的`list-style-type`方法，方法如下：

- `disc`实心圆
- `circle`空心圆
- `square`方块
- `none`无

内部元素使用`<li>`标签。

----

### `<ol>`标签

```HTML
<!DOCTYPE html>
<html>
<body>

<h2>Ordered List with Numbers</h2>

<ol type="1">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ol>

<ol type="A">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ol>

<ol type="a">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ol>

<ol type="I">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ol>

<ol type="i">
  <li>Coffee</li>
  <li>Tea</li>
  <li>Milk</li>
</ol>

</body>
</html>
```
效果:

![sp160807_165320](http://ooo.0o0.ooo/2016/08/07/57a6f72c7f75a.png)

<!-- ![sp160807_165320](/assets/sp160807_165320.png) -->

----

`<ol>`中可以使用`type`显示序号，常用方法如下:

- `1`数字序号
- `A`大写英文字母序号
- `a`小写英文字母序号
- `I`大写罗马字母序号
- `i`小写罗马字母序号

内部元素使用`<li>`标签。

----

### `<dl>`标签

- `<dl>`用来创建一个描述列表，结合`<dt>`与`<dd>`使用。
- `<dt>`用来创建描述名
- `<dd>`用来创建描述内容

```HTML
<dl>
  <dt>Coffee</dt>
  <dd>- black hot drink</dd>
  <dt>Milk</dt>
  <dd>- white cold drink</dd>
</dl>
```
效果

![sp160807_172307](http://ooo.0o0.ooo/2016/08/07/57a6fe243b1cb.png)

<!-- ![sp160807_172307](/assets/sp160807_172307.png) -->

----

### `<li>`标签

在`<li>`标签中设定`float:left`或者`float:right`使标题横向显示。

![sp160807_171736](http://ooo.0o0.ooo/2016/08/07/57a6fd336cc63.png)

<!-- ![sp160807_171736](/assets/sp160807_171736.png) -->

----
