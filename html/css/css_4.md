<!--toc-->

- [CSS 连接、列表、表格](#css-连接-列表-表格)
	- [CSS 连接](#css-连接)
		- [Styling Links](#styling-links)
		- [Text 文本线](#text-文本线)
		- [Background Color](#background-color)
		- [高级链接按钮](#高级链接按钮)
	- [CSS 列表](#css-列表)
		- [list-style-position](#list-style-position)
	- [CSS Tables](#css-tables)
		- [Table Borders](#table-borders)
		- [Collapse Table Borders](#collapse-table-borders)
		- [Table Width and Height](#table-width-and-height)
		- [Horizontal Alignment](#horizontal-alignment)
		- [Vertical Alignment](#vertical-alignment)
		- [Table Padding](#table-padding)
		- [Horizontal Dividers](#horizontal-dividers)
		- [Hoverable Table](#hoverable-table)
		- [Table Color](#table-color)
		- [Responsive Table](#responsive-table)

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

## CSS Tables

### Table Borders

### Collapse Table Borders

### Table Width and Height

### Horizontal Alignment

### Vertical Alignment

### Table Padding

### Horizontal Dividers

### Hoverable Table

###　Striped Tables

### Table Color

### Responsive Table
