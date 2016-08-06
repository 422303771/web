<!-- toc -->

- [HTML5 基本基础](#html5-基本基础)
	- [HTML5 基本页面构成](#html5-基本页面构成)
	- [HTML版本与年代](#html版本与年代)
	- [HTML 编辑方法](#html-编辑方法)
	- [HTML 标签](#html-标签)
		- [`<head>`标签](#head标签)
		- [`<h1>`到`<h6>`标签](#h1到h6标签)
		- [`<p>`标签](#p标签)
			- [`<p>`标签属性](#p标签属性)
				- [title](#title)
		- [`<pre>`标签](#pre标签)
		- [`<a>`标签](#a标签)
			- [`<a>`标签属性](#a标签属性)
				- [href](#href)
				- [size](#size)
				- [alt](#alt)
		- [`<image>`标签](#image标签)
		- [`<hr>`标签](#hr标签)
		- [`<br>`标签](#br标签)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# HTML5 基本基础

## HTML5 基本页面构成

2014 年后在网页的构造上开始使用HTML5标准。

```html
<!DOCTYPE html>
<html>
<head>
<title>网页标题</title>
</head>
<body>

<h1>My First Heading</h1>
<p>My first paragraph.</p>

</body>
</html>
```

- `<!DOCTYPE html>`指明文件类型。
- `<html></html>`之间是页面主体。
- `<head></head>`之间是头部信息，编写页面描述，载入CSS与JS等等。
- `<body></body>`之间是页面的可见内容。
- `<h1></h1>`是网页的标题。
- `<p></p>`是网页的文字内容。

下图是网页运行效果。

![sp160806_133729](http://ooo.0o0.ooo/2016/08/06/57a577c109d93.png)

<!-- ![sp160806_133729](/assets/sp160806_133729.png) backup -->

 下图是一个可视化的HTML结构。

![sp160806_134000](http://ooo.0o0.ooo/2016/08/06/57a578574a733.png)

<!-- ![sp160806_134000](/assets/sp160806_134000.png) backup -->

 --------------------------------------------------------------------------------

## HTML版本与年代

版本        | 制定年代
--------- | ----
HTML      | 1991
HTML 2.0  | 1995
HTML 3.2  | 1997
HTML 4.01 | 1999
XHTML     | 2000
HTML      | 2014

--------------------------------------------------------------------------------

## HTML 编辑方法

使用任意的文本编辑器，`microsoft office`系列除外。

--------------------------------------------------------------------------------

## HTML 标签

### `<head>`标签

`HTML`文件的头部说明，一般包含`<title>`与`<meta charset="UTF-8">`，指定网页标题与字符集。还有其他的等等。 不显示在页面中。

```html
<!DOCTYPE html>
<html>
<head>
  <title>My First HTML</title>
  <meta charset="UTF-8">
</head>
<body>

<p>The HTML head element contains meta data.</p>
<p>Meta data is data about the HTML document.</p>

</body>
</html>
```

--------------------------------------------------------------------------------

### `<h1>`到`<h6>`标签

**标题标签**

```html
<!DOCTYPE html>
<html>
<body>

<h1>This is heading 1</h1>
<h2>This is heading 2</h2>
<h3>This is heading 3</h3>
<h4>This is heading 4</h4>
<h5>This is heading 5</h5>
<h6>This is heading 6</h6>

</body>
</html>
```

效果：

![sp160806_134937](http://ooo.0o0.ooo/2016/08/06/57a57a97d3566.png)

<!-- ![sp160806_134937](/assets/sp160806_134937.png) backup -->

 --------------------------------------------------------------------------------

### `<p>`标签

**文本标签**

```html
<!DOCTYPE html>
<html>
<body>

<p>This is a paragraph.</p>
<p>This is a paragraph.</p>
<p>This is a paragraph.</p>

</body>
</html>
```

效果：

![sp160806_135341](http://ooo.0o0.ooo/2016/08/06/57a57b8f733f1.png)

<!-- ![sp160806_135341](/assets/sp160806_135341.png) backup -->

 #### `<p>`标签属性

##### title

当鼠标移动到段落是，显示title中内容，使用方法如下：

```html
<p title="I'm a tooltip"></p>
```

-----

### `<pre>`标签

`<pre>`标签保留文本<kbd>Space</kbd>与<kbd>Enter</kbd>。

```HTML
<!DOCTYPE html>
<html>
<body>

<p>The pre tag preserves both spaces and line breaks:</p>

<pre>
   My Bonnie lies over the ocean.

   My Bonnie lies over the sea.

   My Bonnie lies over the ocean.

   Oh, bring back my Bonnie to me.
</pre>

</body>
</html>

```
效果：

![sp160806_150540](http://ooo.0o0.ooo/2016/08/06/57a58caaaefd8.png)

<!-- ![sp160806_150540](/assets/sp160806_150540.png) -->


--------------------------------------------------------------------------------

### `<a>`标签

**:link:连接标签**

```html
<!DOCTYPE html>
<html>
<body>

<a href="http://www.w3schools.com">This is a link</a>

</body>
</html>
```

产出一个快速连接。:link:

#### `<a>`标签属性

##### href

指定连接地址，用法如下：

```html
<a href="http://www.w3schools.com">This is a link</a>
```

##### size

指定图片大小，使用`width`与`height`，下方是例子：

```html
<img src="w3schools.jpg" width="104" height="142">
```

##### alt

指定图片不能显示时的显示文本，用法如下：

```html

<img src="w3sch.jpg" alt="W3Schools.com" width="304" height="142">
```

效果如下：

![sp160806_142710](http://ooo.0o0.ooo/2016/08/06/57a583654e81b.png)

<!-- ![sp160806_142710](/assets/sp160806_142710.png) -->

 --------------------------------------------------------------------------------

### `<image>`标签

**图片标签**

```html
<!DOCTYPE html>
<html>
<body>

<img src="w3schools.jpg" alt="W3Schools.com" width="104" height="142">

</body>
</html>
```

效果：

![sp160806_140716](http://ooo.0o0.ooo/2016/08/06/57a57eba2728e.png)

<!-- ![sp160806_140716](/assets/sp160806_140716.png) backup -->

 --------------------------------------------------------------------------------

_注意_

- 标签一定要成对出现。

- 在HTML的标签属性中可以不使用`" "`,但是一个属性中有<kbd>Space</kbd>时，还是要使用`" "`。一些特殊情况可以混有`" "`与`' '`。

--------------------------------------------------------------------------------

### `<hr>`标签

产生一条分隔线.

![sp160806_145116](http://ooo.0o0.ooo/2016/08/06/57a5890cdd0c7.png)

<!-- ![sp160806_145116](/assets/sp160806_145116.png) -->

### `<br>`标签

产生换行。
