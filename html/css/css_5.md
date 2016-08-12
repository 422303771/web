<!--toc-->

- [CSS 显示方法](#css-显示方法)
	- [`display `](#display)
		- [设定内容点击显示](#设定内容点击显示)
		- [`inline`](#inline)
	- [`visibility`](#visibility)
	- [`max-width` 最大宽度](#max-width-最大宽度)
	- [`position`位置属性](#position位置属性)
		- [`static`](#static)
		- [`fixed`](#fixed)
		- [`relative`](#relative)
		- [`absolute`](#absolute)
	- [`z-index`](#z-index)
	- [例子](#例子)
	- [`cursor`鼠标移入样式](#cursor鼠标移入样式)
	- [`clip`剪切](#clip剪切)
	- [`overflow` 溢出](#overflow-溢出)
	- [`float`与`clear`](#float与clear)
	- [`margin:auto`](#marginauto)
	- [`opacity`设置透明度。](#opacity设置透明度)
	- [`input`输入框设置](#input输入框设置)
		- [设置输入时背景色](#设置输入时背景色)
		- [设置输入框边框](#设置输入框边框)
		- [设置有图标的输出框](#设置有图标的输出框)
		- [设置有动画效果的输入框](#设置有动画效果的输入框)
		- [设置文本输入框样式](#设置文本输入框样式)
		- [设置下拉选择](#设置下拉选择)
		- [设置按钮](#设置按钮)

<!-- tocstop -->

----

# CSS 显示方法

## `display `

### 设定内容点击显示

通过`JS`设置display的`none`或`block`实现。

当设置为`display:none`时，设置样式为隐藏。

当设置为`display:block`时，设置样式为显示。

_注意：这种隐藏方法不占用页面样式位置。_

### `inline`

设定内容在一行显示。

```CSS
span#mySpan {
    background-color:red;
    display:inline;
}
```
![sp160812_175427](http://ooo.0o0.ooo/2016/08/12/57ad9d1d5f444.png)

<!-- ![sp160812_175427](/assets/sp160812_175427.png) -->

```CSS
span#mySpan {
    background-color:red;
    display:block;
}
```

![sp160812_175526](http://ooo.0o0.ooo/2016/08/12/57ad9d426af64.png)

<!-- ![sp160812_175526](/assets/sp160812_175526.png) -->

----

## `visibility`

隐藏或显示样式，可用选项:

- `visible`显示
- `hidden`隐藏

_注意：隐藏后，样式的位置不会消失，仅内容隐藏。_

```CSS
div#myDIV {
    background-color:yellow;
    visibility:hidden;
}
```

---

## `max-width` 最大宽度

`max-width`在页面缩放时，依然能全部显示在窗口内。

使用`width`时，当设置低于设定值，会有溢出。

![sp160812_180037](http://ooo.0o0.ooo/2016/08/12/57ad9e7b8f66b.png)

<!-- ![sp160812_180037](/assets/sp160812_180037.png) -->

---

## `position`位置属性

`position`会根据

- `static`静态
- `fixed`固定
- `relative`相对
- `absolute`绝对

`top`、`bottom`、`left`、`right`属性会根据`position`设置的不同而改变。

### `static`

根据页面位置定位不需要特殊设置。

### `fixed`

根据`top`、`bottom`、`left`、`right`四项设置位置，位置不随页面大小改变。

当溢出时，不触发滚动条，适合设置`固定于页面角落的元素`。

### `relative`

根据`top`、`bottom`、`left`、`right`四项设置位置，位置随页面大小改变。

当有元素出现在设定位置时，将自动调整元素位置。

### `absolute`

`absolute`的定位决定于`父元素`而非页面位置。当`父元素`不存在时，已页面位置为准。

---

## `z-index`

`z-index`用于设置元素的显示层，设定为负值在标准元素后，正值为标准元素之前。

```CSS
img {
    position: absolute;
    left: 0px;
    top: 0px;
    z-index: -1;
}
```
---

## 例子

设置文字相对于图片的位置。

```CSS
.container {
    position: relative;
}

.topleft {
    position: absolute;
    top: 8px;
    left: 16px;
    font-size: 18px;
}

img {
    width: 100%;
    height: auto;
    opacity: 0.3;
}
```

![sp160812_184428](http://ooo.0o0.ooo/2016/08/12/57ada8c81a94b.png)

<!-- ![sp160812_184428](/assets/sp160812_184428.png) -->

---

## `cursor`鼠标移入样式

[详细页面](http://www.w3schools.com/cssref/pr_class_cursor.asp)

---

## `clip`剪切

[详细页面](http://www.w3schools.com/cssref/pr_pos_clip.asp)

----

## `overflow` 溢出


设置滚动条，常用设置项：

- `visible`显示
- `hidden `隐藏
- `scroll`显示滚动外框，当需要时，显示滚动条
- `auto`自动

下方的设置，同样可以使用上方的属性。

- `overflow-x`横向滚动条
- `overflow-y`竖向滚动条

---

## `float`与`clear`

`float`指明元素是否应该浮动。

`clear`用于控制浮动元素行为。用于防止设定的元素绕开`float`,确保布局的正确。

设置`overflow: auto`，有效控制边框内容溢出。

处理横向多元素时，可以使用`display: inline-block`来代替`float:left`，使元素自动适应页面大小。



---


## `margin:auto`

当`margin:auto`为自动时，被设定元素自动居中。

---

## `opacity`设置透明度。

可以用在`img`、`div`等标签中。

----

## `input`输入框设置

`input[type=text]`设置输入框效果。

```CSS
input[type=text] {
    background-color: #3CBC8D;
    color: white;
}
```
![sp160812_233839](http://ooo.0o0.ooo/2016/08/12/57adedb8893cd.png)

<!-- ![sp160812_233839](/assets/sp160812_233839.png) -->


### 设置输入时背景色

```CSS
input[type=text]:focus {
    background-color: lightblue;
}
```
![sp160812_234001](http://ooo.0o0.ooo/2016/08/12/57adee0c5014d.png)

<!-- ![sp160812_234001](/assets/sp160812_234001.png) -->

### 设置输入框边框

```CSS
input[type=text]:focus {
    border: 3px solid #555;
}
```
![sp160812_234057](http://ooo.0o0.ooo/2016/08/12/57adee3fd967b.png)

<!-- ![sp160812_234057](/assets/sp160812_234057.png) -->

### 设置有图标的输出框

```CSS
input[type=text] {
    width: 100%;
    box-sizing: border-box;
    border: 2px solid #ccc;
    border-radius: 4px;
    font-size: 16px;
    background-color: white;
    background-image: url('searchicon.png');
    background-position: 10px 10px;
    background-repeat: no-repeat;
    padding: 12px 20px 12px 40px;
}
```
```HTML
<form>
  <input type="text" name="search" placeholder="Search..">
</form>

```
![sp160812_234259](http://ooo.0o0.ooo/2016/08/12/57adeeba4a587.png)

<!-- ![sp160812_234259](/assets/sp160812_234259.png) -->

### 设置有动画效果的输入框

`CSS3`增加

```CSS
input[type=text] {
    width: 130px;
    box-sizing: border-box;
    border: 2px solid #ccc;
    border-radius: 4px;
    font-size: 16px;
    background-color: white;
    background-image: url('searchicon.png');
    background-position: 10px 10px;
    background-repeat: no-repeat;
    padding: 12px 20px 12px 40px;
    -webkit-transition: width 0.4s ease-in-out;
    transition: width 0.4s ease-in-out;
}

input[type=text]:focus {
    width: 100%;
}
```
![sp160812_234642](http://ooo.0o0.ooo/2016/08/12/57adef9a13805.png)

![sp160812_234611](http://ooo.0o0.ooo/2016/08/12/57adef8570dda.png)

<!-- ![sp160812_234642](/assets/sp160812_234642.png) -->

<!-- ![sp160812_234611](/assets/sp160812_234611.png) -->

### 设置文本输入框样式

```CSS
textarea {
    width: 100%;
    height: 150px;
    padding: 12px 20px;
    box-sizing: border-box;
    border: 2px solid #ccc;
    border-radius: 4px;
    background-color: #f8f8f8;
    font-size: 16px;
    resize: none;
}
```
```HTML
<form>
  <textarea>Some text...</textarea>
</form>
```
![sp160812_234843](http://ooo.0o0.ooo/2016/08/12/57adf016215c5.png)

<!-- ![sp160812_234843](/assets/sp160812_234843.png) -->

----

### 设置下拉选择

```CSS
select {
    width: 100%;
    padding: 16px 20px;
    border: none;
    border-radius: 4px;
    background-color: #f1f1f1;
}
```
```HTML
<form>
  <select id="country" name="country">
    <option value="usa">Australia</option>
    <option value="usa">Canada</option>
    <option value="usa">USA</option>
  </select>
</form>
```
![sp160812_235013](http://ooo.0o0.ooo/2016/08/12/57adf0a313450.png)

<!-- ![sp160812_235013](/assets/sp160812_235013.png) -->

---

###  设置按钮

```CSS
input[type=button], input[type=submit], input[type=reset] {
    background-color: #4CAF50;
    border: none;
    color: white;
    padding: 16px 32px;
    text-decoration: none;
    margin: 4px 2px;
    cursor: pointer;
}
```

```HTML
<input type="button" value="Button">
<input type="reset" value="Reset">
<input type="submit" value="Submit">
```
![sp160812_235246](http://ooo.0o0.ooo/2016/08/12/57adf1046bced.png)

<!-- ![sp160812_235246](/assets/sp160812_235246.png) -->


---
