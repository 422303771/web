<!--toc-->

- [CSS 连接、列表、表格](#css-连接-列表-表格)
	- [CSS 连接](#css-连接)
		- [Styling Links](#styling-links)
		- [Text 文本线](#text-文本线)
		- [Background Color](#background-color)
		- [高级链接按钮](#高级链接按钮)
	- [CSS 列表](#css-列表)
		- [list-style-position](#list-style-position)
	- [CSS 表单](#css-表单)
		- [`border-collapse`设置表格边框。](#border-collapse设置表格边框)
		- [`width`与`height`](#width与height)
		- [`vertical-align`文本对齐方式](#vertical-align文本对齐方式)
		- [`Padding`](#padding)
		- [`border`边框](#border边框)
		- [`tr:hover`设置鼠标移入时效果](#trhover设置鼠标移入时效果)
		- [`tr:nth-child(even)`设置隔行](#trnth-childeven设置隔行)
		- [`background-color`表单背景色](#background-color表单背景色)
		- [`overflow-x:auto`设定滚动条](#overflow-xauto设定滚动条)
		- [`caption-side`表格标题](#caption-side表格标题)

<!-- tocstop -->

----

# CSS 连接、列表、表格

## CSS 连接

### Styling Links

`<a>`的属性

- `a:link`正常链接，没有访问时。
- `a:visited`访问过的链接
- `a:hover`当鼠标移动到链接上时
- `a:active`当链接被点击时

```CSS
/* unvisited link */
a:link {
    color: red;
}

/* visited link */
a:visited {
    color: green;
}

/* mouse over link */
a:hover {
    color: hotpink;
}

/* selected link */
a:active {
    color: blue;
}
```

### Text 文本线

与文本的设定方式相同。

`text-decoration`属性

- `none`没有线
- `underline`下划线
- `overline`上划线
- `line-through`删除线

### Background Color

设置链接背景色

```CSS
a:link {
    background-color: yellow;
}

a:visited {
    background-color: cyan;
}

a:hover {
    background-color: lightgreen;
}

a:active {
    background-color: hotpink;
}
```

使用`background-color`

### 高级链接按钮

```CSS
a:link, a:visited {
    background-color: #f44336;
    color: white;
    padding: 14px 25px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
}

a:hover, a:active {
    background-color: red;
}
```

效果如下：

![sp160811_212257](http://ooo.0o0.ooo/2016/08/11/57ac7c681c0ee.png)

<!-- ![sp160811_212257](/assets/sp160811_212257.png) -->

---

## CSS 列表

`list-style`的设置方法用下面几种

- `list-style-type` 列表前方样式
- `list-style-image`
	- `url`设置样式图片链接
	- `none`无
- `list-style-position`
	- `inside`设置缩进，缩进的距离比较大
	- `outside`设置缩进，缩进的距离比较小

使用`list-style`简写


`<ol>`的`list-style-type`有序排列

- `none`无
- `armenian`亚美尼亚
- `cjk-ideographic`中日韩大小字母
- `decimal`十进制
- `decimal-leading-zero`十进制零补位
- `georgian`格鲁吉亚语
- `hebrew`希伯来语
- `hiragana`平假名
- `hiragana-iroha`平假名，伊吕波
- `katakana`片假名
- `katakana-iroha`片假名，伊吕波
- `lower-alpha`小写英文
- `lower-greek`小写希腊语
- `lower-latin`小写拉丁文
- `lower-roman`小写罗马数字
- `upper-alpha`大写英文
- `upper-latin`大写拉丁文
- `upper-roman`大写罗马数字

`<ul>`的`list-style-type`无序排列

- `none`无
- `disc`实心圆
- `circle`空心圆


HTML中使用的列表标签有两种`<ul>`无顺序列表与`<ol>`有顺序列表。

### list-style-position

```CSS
ul.a {
    list-style-position: inside;
}

ul.b {
    list-style-position: outside;
}
```

![sp160811_220845](http://ooo.0o0.ooo/2016/08/11/57ac872842e6a.png)

<!-- ![sp160811_220845](/assets/sp160811_220845.png) -->

---

## CSS 表单

主要控制`<table>`、`<th>`、`<td>`、`<tr>`标签。默认情况为双线分格。

- `<table>`表格主体
- `<tr>`表格行
- `<th>`表格头
- `<td>`正常的表格内容

```CSS
table, th, td {
   border: 1px solid black;
}
```

![sp160812_134940](http://ooo.0o0.ooo/2016/08/12/57ad63ad9528f.png)

<!-- ![sp160812_134940](/assets/sp160812_134940.png) -->

### `border-collapse`设置表格边框。

可以设置的属性：

- `collapse`单线
- `separate`双线，默认设置。

![sp160812_160501](http://ooo.0o0.ooo/2016/08/12/57ad83674a112.png)

<!-- ![sp160812_160501](/assets/sp160812_160501.png) -->

如想要达到下方效果

![sp160812_160606](http://ooo.0o0.ooo/2016/08/12/57ad83a47d2e2.png)

<!-- ![sp160812_160606](/assets/sp160812_160606.png) -->

设置如下:

```CSS
table {
    border: 1px solid black;
}
```

### `width`与`height`

使用`width`与`height`设定表格宽度与大小。

```CSS
table, td, th {
    border: 1px solid black;
}

table {
    border-collapse: collapse;
    width: 100%;
}

th {
    height: 50px;
}
tr {
    height: 50px;
    text-align:center
}
```
效果:

![sp160812_165816](http://ooo.0o0.ooo/2016/08/12/57ad8fde419fe.png)

<!-- ![sp160812_165816](/assets/sp160812_165816.png) -->

图中使用`text-align`设定文本位置。

### `vertical-align`文本对齐方式

设置方式为`top`、`bottom`、`middle`。

```CSS
td {
    height: 50px;
    vertical-align: bottom;
}
```

![sp160812_170249](http://ooo.0o0.ooo/2016/08/12/57ad90ed2d889.png)

<!-- ![sp160812_170249](/assets/sp160812_170249.png) -->

### `Padding`

设置文本到边框距离

```CSS
th, td {
    padding: 15px;
    text-align: left;
}
```

![sp160812_170438](http://ooo.0o0.ooo/2016/08/12/57ad915ca43ed.png)

<!-- ![sp160812_170438](/assets/sp160812_170438.png) -->

### `border`边框

`border`设置边框的样式与大小。设置方法通用。

```CSS
table {
    border-collapse: collapse;
    width: 100%;
}

th, td {
    padding: 8px;
    text-align: left;
    border-bottom: 1px solid #ddd;
}
```

![sp160812_170708](http://ooo.0o0.ooo/2016/08/12/57ad91f3135de.png)

<!-- ![sp160812_170708](/assets/sp160812_170708.png) -->

### `tr:hover`设置鼠标移入时效果

```CSS
tr:hover {background-color: #f5f5f5}
```
![sp160812_170839](http://ooo.0o0.ooo/2016/08/12/57ad924fedf17.png)

<!-- ![sp160812_170839](/assets/sp160812_170839.png) -->

### `tr:nth-child(even)`设置隔行

```CSS
tr:nth-child(even) {background-color: #f2f2f2}
```

![sp160812_171013](http://ooo.0o0.ooo/2016/08/12/57ad92ab8682f.png)

<!-- ![sp160812_171013](/assets/sp160812_171013.png) -->

### `background-color`表单背景色

```CSS
th {
    background-color: #4CAF50;
    color: white;
}
```

![sp160812_171127](http://ooo.0o0.ooo/2016/08/12/57ad92f852ad2.png)

<!-- ![sp160812_171127](/assets/sp160812_171127.png) -->


###  `overflow-x:auto`设定滚动条

当页面过小时，使用滚动条，方便看内容。

```HTML
<div style="overflow-x:auto;">

<table>
... table content ...
</table>

</div>
```

![sp160812_171434](http://ooo.0o0.ooo/2016/08/12/57ad93bf732c4.png)

<!-- ![sp160812_171434](/assets/sp160812_171434.png) -->

### `caption-side`表格标题

设置表格标题位置`top`、`bottom`,设置在`<table>`标签下。

```HTML
<caption>Table 1.1 Customers</caption>
```

```CSS
table#myTable {
    caption-side:top;
}
```

![sp160812_172819](http://ooo.0o0.ooo/2016/08/12/57ad96ea5b640.png)

<!-- ![sp160812_172819](/assets/sp160812_172819.png) -->
