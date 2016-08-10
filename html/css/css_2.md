<!--toc-->

- [CSS Box Model](#css-box-model)
	- [CSS Margins](#css-margins)
	- [CSS Padding](#css-padding)
	- [CSS Height&Width](#css-heightwidth)
	- [CSS Outline](#css-outline)
		- [outline-style](#outline-style)
		- [outline-color](#outline-color)
		- [outline-width](#outline-width)
		- [outline简写](#outline简写)

<!-- tocstop -->

----

# CSS Box Model

![sp160810_002236](http://ooo.0o0.ooo/2016/08/09/57aa037c9fff8.png)

<!-- ![sp160810_002236](/assets/sp160810_002236.png) -->

盒子大小的计算方法。

```CSS
div {
    width: 320px;
    padding: 10px;
    border: 5px solid gray;
    margin: 0;
}
```
实际像素为：

```
320px (width)
+ 20px (left + right padding)
+ 10px (left + right border)
+ 0px (left + right margin)
= 350px
```

----


## CSS Margins

如上图所见，用来设置元素的外部边距。

- `Margin`设置边框距离。可以是统一的1个值，也可以是4个独立值。
- `margin-top`顶部边框距离
- `margin-bottom`底部边框距离
- `margin-left`左边框距离
- `margin-right`右边框距离

`margin`的赋值方法如下：
- `auto`自动
- `px`像素
- `%`百分比
- `inherit`继承上级元素值。

```CSS
p {
    margin-top: 100px;
    margin-bottom: 100px;
    margin-right: 150px;
    margin-left: 80px;
}
```

---


## CSS Padding

内容中心到边框的距离，常用设置方法如下。

- `padding`设置内容到边框距离。可以是统一的1个值，也可以是4个独立值。
- `padding-top`顶部距离
- `padding-bottom`底部距离
- `padding-left`左距离
- `padding-right`右距离

常用属性如下：

- `px`像素距离。
- `%`百分比距离。

```CSS
p {
    padding-top: 50px;
    padding-right: 30px;
    padding-bottom: 50px;
    padding-left: 80px;
}
```


---



## CSS Height&Width

可以用来设置任意的高度与宽度,设置方法如下：

- `Height`高度
- `min-height`最小高度
- `max-height`最大高度
- `width`宽度
- `min-width`最小宽度
- `max-width`最大宽度

可以选择的设置方法：

- `auto`自动
- `px`像素
- `%`百分比

```CSS
div {
    max-width: 500px;
    height: 100px;
    background-color: powderblue;
}
```

---


## CSS Outline

![sp160810_125957](http://ooo.0o0.ooo/2016/08/10/57aab50af08e0.png)

<!-- ![sp160810_125957](/assets/sp160810_125957.png) -->

设置边框外边框，常用设置属性如下：

_注意：外边框会在`margin`之内。_


### outline-style

```CSS
p {
    border: 1px solid black;
    outline-color: red;
}

p.dotted {outline-style: dotted;}
p.dashed {outline-style: dashed;}
p.solid {outline-style: solid;}
p.double {outline-style: double;}
p.groove {outline-style: groove;}
p.ridge {outline-style: ridge;}
p.inset {outline-style: inset;}
p.outset {outline-style: outset;}
```
![sp160810_130424](http://ooo.0o0.ooo/2016/08/10/57aab6092dd42.png)

<!-- ![sp160810_130424](/assets/sp160810_130424.png) -->

### outline-color

```CSS
p {
    border: 1px solid black;
    outline-style: double;
    outline-color: red;
}
```

### outline-width

设置外边框宽度,设置方法如下：

- `medium`中等宽度
- `thin`瘦宽度
- `thick`厚宽度
- `px`像素

```CSS
p {border: 1px solid black;}

p.one {
    outline-style: double;
    outline-color: red;
    outline-width: thick;
}

p.two {
    outline-style: double;
    outline-color: green;
    outline-width: 3px;
}
```
![sp160810_131012](http://ooo.0o0.ooo/2016/08/10/57aab76654f89.png)

<!-- ![sp160810_131012](/assets/sp160810_131012.png) -->

### outline简写

顺序分别是`width`、`style`、`color`

```CSS
p {
    border: 1px solid black;
    outline: 5px dotted red;
}
```
