<!-- toc -->

- [HTML5](#html5)
	- [HTML5 基本页面构成](#html5-基本页面构成)
	- [HTML版本与年代](#html版本与年代)
	- [HTML 编辑方法](#html-编辑方法)
	- [HTML 标签](#html-标签)
		- [`<h1>`到`<h6>`标签](#h1到h6标签)
			- [`<p>`标签](#p标签)
		- [`<a>`标签](#a标签)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# HTML5

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

![sp160806_133729](http://ooo.0o0.ooo/2016/08/06/57a577c109d93.png) <!-- ![sp160806_133729](/assets/sp160806_133729.png) backup -->

下图是一个可视化的HTML结构。

![sp160806_134000](http://ooo.0o0.ooo/2016/08/06/57a578574a733.png) <!-- ![sp160806_134000](/assets/sp160806_134000.png) backup -->

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

![sp160806_134937](http://ooo.0o0.ooo/2016/08/06/57a57a97d3566.png) <!-- ![sp160806_134937](/assets/sp160806_134937.png) backup -->

#### `<p>`标签

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

![sp160806_135341](http://ooo.0o0.ooo/2016/08/06/57a57b8f733f1.png) <!-- ![sp160806_135341](/assets/sp160806_135341.png) backup -->

### `<a>`标签

**:link:连接标签**
