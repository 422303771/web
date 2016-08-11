<!--toc-->

- [CSS 文本与字体](#css-文本与字体)
	- [CSS 文本](#css-文本)
		- [Text 颜色](#text-颜色)
		- [Text 位置](#text-位置)
		- [Text 装饰](#text-装饰)
		- [Text 大小写](#text-大小写)
		- [Text 缩进](#text-缩进)
		- [Letter 文字间距](#letter-文字间距)
		- [文字行间距](#文字行间距)
		- [Text 文本阅读方向](#text-文本阅读方向)
		- [Word Spacing 词间距](#word-spacing-词间距)
		- [CSS3 Text](#css3-text)
			- [Overflow 文本溢出](#overflow-文本溢出)
			- [Word Wrapping 词换行](#word-wrapping-词换行)
			- [Word Breaking 自动断字](#word-breaking-自动断字)
	- [CSS Fonts](#css-fonts)
		- [Font Family](#font-family)
		- [Font Style](#font-style)
		- [Font Size](#font-size)
		- [Set Font Size](#set-font-size)
			- [Pixels](#pixels)
			- [Em](#em)
			- [Use a Combination of Percent and Em](#use-a-combination-of-percent-and-em)
		- [Font Weight](#font-weight)
		- [Font Variant](#font-variant)
		- [CSS3 Fonts](#css3-fonts)
			- [Bold Text](#bold-text)
			- [CSS3 Font Descriptors](#css3-font-descriptors)

<!-- tocstop -->

----

# CSS 文本与字体

## CSS 文本

### Text 颜色

```CSS
body {
    color: blue;
}

h1 {
    color: green;
}
```
蓝色与绿色的字体。

### Text 位置

```CSS
h1 {
    text-align: center;
}

h2 {
    text-align: left;
}

h3 {
    text-align: right;
}
```
`center`字体位于中心、`right`字体位于右边、`left`字体位于左边

`justify`拉伸，使左右边缘是直的。

### Text 装饰

```CSS
h1 {
    text-decoration: overline;
}

h2 {
    text-decoration: line-through;
}

h3 {
    text-decoration: underline;
}
```
`overline`上划线、`line-through`删除线、`underline`下划线、

`none`无线，可以隐藏连接的下划线。

### Text 大小写

```CSS
p.uppercase {
    text-transform: uppercase;
}

p.lowercase {
    text-transform: lowercase;
}

p.capitalize {
    text-transform: capitalize;
}
```
`uppercase`全部大写、`lowercase`全部小写、`capitalize`仅首字母大小。

### Text 缩进

```CSS
p {
    text-indent: 50px;
}
```
设置首行缩进。

### Letter 文字间距

```CSS
h1 {
    letter-spacing: 3px;
}
h2 {
    letter-spacing: -3px;
}
```

![sp160811_124105](http://ooo.0o0.ooo/2016/08/10/57ac02189c1f6.png)

<!-- ![sp160811_124105](/assets/sp160811_124105.png) -->

设置文字间距，上图是`+3px`与`-3px`效果。

### 文字行间距

```CSS
p.small {
    line-height: 0.7;
}

p.big {
    line-height: 1.8;
}
```
默认行间距大约为`1.1`到`1.2`.下图为`0.7`与`1.8`的效果。

![sp160811_132913](http://ooo.0o0.ooo/2016/08/11/57ac0d5adaec2.png)

<!-- ![sp160811_132913](/assets/sp160811_132913.png) -->

### Text 文本阅读方向

```CSS
div.ex1 {
    direction: rtl;
}
```
默认情况是从左到右，例子中设定为从右到左。

### Word Spacing 词间距

```CSS
h1 {
    word-spacing: 10px;
}

h2 {
    word-spacing: -5px;
}
```
调整单词与单词间距离，对中文的作用不大。

对文本中<kbd>spacebar</kbd>有效

### CSS3 Text

#### Overflow 文本溢出

```CSS
p.test1 {
    white-space: nowrap;
    width: 200px;
    border: 1px solid #000000;
    overflow: hidden;
    text-overflow: clip;
}

p.test2 {
    white-space: nowrap;
    width: 200px;
    border: 1px solid #000000;
    overflow: hidden;
    text-overflow: ellipsis;
}
```
先设置`overflow`为`hidden`。意思为当文本溢出时，隐藏多余的。

`text-overflow`可以设置为:

- `ellipsis`结尾处使用`……`
- `clip` 结尾处显示剩余文本，直到空间结束。

![sp160811_140502](http://ooo.0o0.ooo/2016/08/11/57ac15c0314a6.png)

<!-- ![sp160811_140502](/assets/sp160811_140502.png) -->

#### Word Wrapping 词换行

```CSS
p {
    word-wrap: break-word;
}
```
`break-word`使用自动打断任意词并换行、`normal`不打断词换行

![sp160811_142044](http://ooo.0o0.ooo/2016/08/11/57ac19729e6d9.png)

<!-- ![sp160811_142044](/assets/sp160811_142044.png) -->

#### Word Breaking 自动断字

```CSS
p.test1 {
    word-break: keep-all;
}

p.test2 {
    word-break: break-all;
}
```
`normal`正常情况下不打断词、`keep-all`不打断连续的词、`break-all`在任意位置打断。

![sp160811_141842](http://ooo.0o0.ooo/2016/08/11/57ac18f759e12.png)

<!-- ![sp160811_141842](/assets/sp160811_141842.png) -->


---

## CSS Fonts

### Font Family

### Font Style

### Font Size

### Set Font Size

#### Pixels

#### Em

#### Use a Combination of Percent and Em

### Font Weight

### Font Variant

### CSS3 Fonts

#### Bold Text

#### CSS3 Font Descriptors

---
