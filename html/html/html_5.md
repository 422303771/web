<!-- toc -->

- [HTML 其他](#html-其他)
	- [`<dir>`标签](#dir标签)
	- [定义`class`样式](#定义class样式)
	- [`<Iframes>`标签](#iframes标签)
	- [javascript 调用](#javascript-调用)
	- [`<head>`标签](#head标签)
	- [`layout`网页布局](#layout网页布局)
	- [HTML中常用符号](#html中常用符号)
	- [HTML中的数学符号](#html中的数学符号)
	- [其他字符集](#其他字符集)
	- [常用通信](#常用通信)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# HTML 其他

## `<dir>`标签

HTML中最基本的布局标签。通常配合`ID`或`class`定义样式时使用。

## 定义`class`样式

```html
<!DOCTYPE html>
<html>
<head>
<style>
div.cities {
    background-color: black;
    color: white;
    margin: 20px 0 20px 0;
    padding: 20px;
}
</style>
</head>
<body>

<div class="cities">
<h2>London</h2>
<p>London is the capital of England. It is the most populous city in the United Kingdom, with a metropolitan area of over 13 million inhabitants.</p>
</div>

<div class="cities">
<h2>Paris</h2>
<p>Paris is the capital and most populous city of France.</p>
</div>

<div class="cities">
<h2>Tokyo</h2>
<p>Tokyo is the capital of Japan, the center of the Greater Tokyo Area,
and the most populous metropolitan area in the world.</p>
</div>

</body>
</html>
```

![sp160807_234220](http://ooo.0o0.ooo/2016/08/07/57a7570ce2ed9.png)

<!-- ![sp160807_234220](/assets/sp160807_234220.png) -->

 定义`class`时，在定义的`class`前加`.`。

如果是对特定的`<div>`样式，使用`div.class`格式。

--------------------------------------------------------------------------------

## `<Iframes>`标签

`<Iframes>`标签用于在网页中在载入网页。

```html
<iframe src="demo_iframe.htm" height="200" width="300"></iframe>
```

默认情况下是用边框，手动设置`style`中的`border:none`关闭边框。

当然在`style`中可以设置其他的样式。

特殊的使用方式

```html
<iframe src="demo_iframe.htm" name="iframe_a"></iframe>

<p><a href="http://www.w3schools.com" target="iframe_a">W3Schools.com</a></p>
```

当`<iframe>`中的`name`与`<a>`中的`target`相同时，页面会在`<iframe>`中跳转。

--------------------------------------------------------------------------------

## javascript 调用

现代web应用中必备。通常使用独立文件挂载到`<script>`中。

## `<head>`标签

用于载入网页的`CSS`与`JS`文件，也可以设置一些其他信息。

- `<title>`网页标签
- `<style>`网页样式
- `<link>`网页链接
- `<meta>`指定网页字符集，搜索关键字，作者，网页描述等。
- `<script>`网页JS
- `<base>`网页基本标准制定

`<meta>`参考

`http-equiv`为每三十秒刷新文件，适用于从非本地连接。

```html
<head>
  <meta charset="UTF-8">
  <meta name="description" content="Free Web tutorials">
  <meta name="keywords" content="HTML,CSS,XML,JavaScript">
  <meta name="author" content="Hege Refsnes">
  <meta http-equiv="refresh" content="30">
</head>
```

## `layout`网页布局

- `<header>`定义头部内容
- `<nav>`定义导航
- `<section>`定义部分文件内容
- `<article>`定义独立文章
- `<aside>`预留位置
- `<footer>`最下方文本
- `<details>`定义其他详细信息，不是很常用
- `<summary>`定义一个标题为`<details>`的详细信息，不是很常用

![sp160808_110815](http://ooo.0o0.ooo/2016/08/07/57a8027343b3a.png)

<!-- ![sp160808_110815](/assets/sp160808_110815.png) -->

 _注意：对现代网站而言，使用百分比的布局方式要远远好于写定像素。_

--------------------------------------------------------------------------------

## HTML中常用符号

[详细地址](http://www.w3schools.com/html/html_entities.asp)

符合样式 | 符合解释         | 符号使用名 | 符合使用码
:--- | :----------------- | :------ | :----
|    |non-breaking space  |\&nbsp;  |\&#160;
|<   |less than           |\&lt;    |\&#60;
|>   |greater than        |\&gt;    |\&#62;
|&   |ampersand           |\&amp;   |\&#38;
|¢   |cent                |\&cent;  |\&#162;
|£   |pound               |\&pound; |\&#163;
|¥   |yen                 |\&yen;   |\&#165;
|€   |euro								|\&euro;  |\&#8364;
|©   |copyright           |\&copy;  |\&#169;
|®   |registered trademark|\&reg;   |\&#174;

![sp160808_123742](http://ooo.0o0.ooo/2016/08/07/57a80cca1f9e0.png)

<!-- ![sp160808_123742](/assets/sp160808_123742.png) -->

----

## HTML中的数学符号

[详细地址](http://www.w3schools.com/html/html_symbols.asp)

![sp160808_124014](http://ooo.0o0.ooo/2016/08/07/57a80d7f52c0a.png)

<!-- ![sp160808_124014](/assets/sp160808_124014.png) -->

![sp160808_124030](http://ooo.0o0.ooo/2016/08/07/57a80d8f8a14b.png)

<!-- ![sp160808_124030](/assets/sp160808_124030.png) -->

![sp160808_124050](http://ooo.0o0.ooo/2016/08/07/57a80d9ebe194.png)

<!-- ![sp160808_124050](/assets/sp160808_124050.png) -->

## 其他字符集

包括`ASCII`、`ANST`、`8859`、`UTF-8`比较。

[地址](http://www.w3schools.com/html/html_charset.asp)

## 常用通信

- `HTTP`超文本传输协议，不加密。
- `HTTPS`安全超文本传输协议，加密。
- `FTP`文件传输协议，上传或下载文件。

就现代网络而言要使用`HTTPS`。
