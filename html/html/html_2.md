<!-- toc -->

- [HTML 样式](#html-样式)
	- [`<body>`标签中添加`style`](#body标签中添加style)
	- [其他标签](#其他标签)
	- [设定字体](#设定字体)
	- [设定字体大小](#设定字体大小)
	- [设定字体位置](#设定字体位置)
	- [字体显示位置](#字体显示位置)
	- [HTML中的一些符号](#html中的一些符号)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# HTML 样式

## `<body>`标签中添加`style`

```html
<!DOCTYPE html>
<html>
<body style="background-color:powderblue;">

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

`background-color`为设定背景颜色。

效果：

![sp160806_185405](http://ooo.0o0.ooo/2016/08/06/57a5c1f95fd4f.png)

<!-- ![sp160806_185405](/assets/sp160806_185405.png) -->

 --------------------------------------------------------------------------------

## 其他标签

```html
<!DOCTYPE html>
<html>
<body>

<h1 style="color:blue;">This is a heading</h1>
<p style="color:red;font-size:200%;">This is a paragraph.</p>

</body>
</html>
```

效果：

![sp160806_185806](http://ooo.0o0.ooo/2016/08/06/57a5c2e56ad17.png)

<!-- ![sp160806_185806](/assets/sp160806_185806.png) -->

 --------------------------------------------------------------------------------

## 设定字体

```html
<!DOCTYPE html>
<html>
<body>

<h1 style="font-family:verdana;">This is a heading</h1>
<p style="font-family:courier;">This is a paragraph.</p>

</body>
</html>
```

效果：

![sp160806_185932](http://ooo.0o0.ooo/2016/08/06/57a5c3422f8f6.png)

<!-- ![sp160806_185932](/assets/sp160806_185932.png) -->

 --------------------------------------------------------------------------------

## 设定字体大小

```
<h1 style="font-size:300%;">This is a heading</h1>
```

设定字体大小的为正常的三倍。

--------------------------------------------------------------------------------

## 设定字体位置

```
<h1 style="text-align:center;">Centered Heading</h1>
```

字体居中。还有`right`和`left`

--------------------------------------------------------------------------------

- `style`设置HTML样式
- `background-color`设定背景颜色
- `color`设定字体颜色
- `font-family`设定字体
- `font-size`设定字体大小
- `text-align`设定文本对齐方式

--------------------------------------------------------------------------------

## 字体显示位置

```HTML
<!DOCTYPE html>
<html>
<body>

<p><b>This text is bold</b></p>
<p><i>This text is italic</i></p>
<p>This is<sub> subscript</sub> and <sup>superscript</sup></p>

</body>
</html>
```
效果：

![sp160806_191153](http://ooo.0o0.ooo/2016/08/06/57a5c621003e5.png)

<!-- ![sp160806_191153](/assets/sp160806_191153.png) -->

- `<b>`粗体
- `<i>`斜体
- `<sub>`下脚标
- `<sup>`上脚标
- `<strong>`重要文本与粗体相似
- `<em>`强调文本与斜体相似
- `<mark>`标记文本
- `<small>`缩小字体
- `<del>`文本上使用删除线
- `<ins>`文本上下划线

```HTML
<!DOCTYPE html>
<html>
<body>
<p><b>似懂非懂</b></p>
<strong>似懂非懂</strong><br>
<p><i>似懂非懂</i></p>
<em>似懂非懂</em><br>
<p>This is<sub> 似懂非懂</sub> and <sup>似懂非懂</sup></p>
<mark>似懂非懂</mark> <br>
<small>似懂非懂</small><br>
<del>似懂非懂</del><br>
<ins>似懂非懂</ins><br>
</body>
</html>

```
效果：

![sp160806_193641](http://ooo.0o0.ooo/2016/08/06/57a5cbf0a4a46.png)

<!-- ![sp160806_193641](/assets/sp160806_193641.png) -->

-----

## HTML中的一些符号

- `<q></q>`双引号`""`
- `<blockquote></blockquote>`引用，文本缩进一部分。
- `<abbr title="XX"></abbr>`指明缩写含义。
- `<address></address>`地址，默认斜体显示。
- `<cite></cite>`一般引用，斜体。
- `<bdo></bdo>`


```HTML
<!DOCTYPE html>
<html>
<body>
<q>This line will be written from right to left</q>
<blockquote>This line will be written from right to left</blockquote>
<br>
<abbr title="This line will be written from right to left">TLWBWFRTL</abbr>
<br>
<br>
<address>
This line will <br>
be written from <br>
right to left <br>
</address>
<br>
<br>
<cite>This line will be written from right to left</cite>
<br>
<br>
<bdo dir="rtl">This line will be written from right to left</bdo>
<br>
<br>
<bdo dir="ltr">This line will be written from right to left</bdo>
</body>
</html>
```
效果：

![sp160806_194443](http://ooo.0o0.ooo/2016/08/06/57a5cdf83ba59.png)

<!-- ![sp160806_194443](/assets/sp160806_194443.png) -->
