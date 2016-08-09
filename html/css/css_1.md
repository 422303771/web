<!--toc-->

- [CSS 基础](#css-基础)
	- [CSS 基本语法](#css-基本语法)
	- [CSS 常用定义语句](#css-常用定义语句)
		- [Background](#background)
			- [background-attachment](#background-attachment)
			- [background-color](#background-color)
			- [background-image](#background-image)
			- [background-position](#background-position)
			- [background-repeat](#background-repeat)
			- [background-size](#background-size)
			- [background-origin](#background-origin)
			- [background-clip](#background-clip)
		- [Borders](#borders)
			- [Border Style](#border-style)
			- [Border Width](#border-width)
			- [Border Color](#border-color)
			- [Border - Individual Sides](#border-individual-sides)
			- [Border - Shorthand Property](#border-shorthand-property)
			- [border-radius(CSS3)](#border-radiuscss3)
		- [Border Images(CSS3)](#border-imagescss3)
			- [border-image](#border-image)
			- [border-image-source](#border-image-source)
			- [border-image-slice](#border-image-slice)
			- [border-image-width](#border-image-width)
			- [border-image-outset](#border-image-outset)
			- [border-image-repeat](#border-image-repeat)

<!-- tocstop -->

----

# CSS 基础

## CSS 基本语法

![sp160808_174751](http://ooo.0o0.ooo/2016/08/08/57a85574cd4f1.png)

<!-- ![sp160808_174751](/assets/sp160808_174751.png) -->

## CSS 常用定义语句

### Background

- `background` 声明中包含所有背景属性。
- `background-attachment`设置背景是否随滚动条滚动。
- `background-color`设置背景颜色
- `background-image`设置背景图片
- `background-position`设置背景图片位置
- `background-repeat`设置背景图片重复
_下方为CSS3增加。_
- `background-size`设置背景图片大小
- `background-origin`背景图位置
- `background-clip`背景面积

#### background-attachment

设置背景是否随滚动条滚动，可以选择参数如下。

- `scroll`不随滚动条滚动，默认。
- `fixed`随滚动条滚动
- `local`不随滚动条滚动

```CSS
body {
    background-image: url("img_tree.png");
    background-repeat: no-repeat;
    background-position: right top;
    background-attachment: fixed;
}
```

#### background-color

设置背景色，`transparent`为透明。

```CSS
body {
    background-color: lightblue;
}
```

#### background-image

设置背景图

```CSS
body {
    background-image: url("paper.gif");
}
```

#### background-position

指定图片位置，可设置选项。

- `left``right``top``bottom``center`直接指定位置。
- `x% y%`指定位置百分比。

```CSS
body {
    background-image: url("img_tree.png");
    background-repeat: no-repeat;
    background-position: right top;
}
```

#### background-repeat

可设置选项，

- `repeat`X，Y 方向平铺。
- `repeat-x`X方向平铺。
- `repeat-y`Y方向平铺
- `no-repeat`不平铺
-
```CSS
body {
    background-image: url("gradient_bg.png");
    background-repeat: repeat-x;
}
```
__下方为CCS3增加__

#### background-size

常用设置`px`、`%`、`auto`、`cover`、`contain`。

- `cover`是最大边到左右
- `contain`是最大边到上下

设置图片大小尺寸

```CSS
#div1 {
    background: url(img_flower.jpg);
    background-size: 100px 80px;
    background-repeat: no-repeat;
}
```

#### background-origin

常用方法有`padding-box`、`border-box`、`content-box`。

- `padding-box`在边框内
- `border-box` 在边框边缘
- `content-box`在内容边缘

```CSS
#myDIV {
    padding:25px;
    border:10px dotted #000000;
    background-image:url('smiley.gif');
    background-origin:content-box;
    background-repeat:no-repeat;
}
```

#### background-clip

常用设置与`background-clip`相同。

```CSS
#example1 {
    border: 10px dotted black;
    padding:35px;
    background: yellow;
}

#example2 {
    border: 10px dotted black;
    padding:35px;
    background: yellow;
    background-clip: padding-box;
}

#example3 {
    border: 10px dotted black;
    padding:35px;
    background: yellow;
    background-clip: content-box;
}
```

![sp160809_151015](http://ooo.0o0.ooo/2016/08/09/57a9820ec4212.png)

<!-- ![sp160809_151015](/assets/sp160809_151015.png) -->


-----


### Borders

- `Border-Style`边框样式
- `Border-Width`边框大小
- `Border-Color`边框颜色
- `border-radius`边框圆角

`border`还可以与`right`、`left`、`top`、`bottom`、`Color`、`Width`结合使用。

#### Border Style

```CSS
p.dotted {border-style: dotted;}
p.dashed {border-style: dashed;}
p.solid {border-style: solid;}
p.double {border-style: double;}
p.groove {border-style: groove;}
p.ridge {border-style: ridge;}
p.inset {border-style: inset;}
p.outset {border-style: outset;}
p.none {border-style: none;}
p.hidden {border-style: hidden;}
p.mix {border-style: dotted dashed solid double;}
```
![sp160809_153201](http://ooo.0o0.ooo/2016/08/09/57a9874301288.png)

<!-- ![sp160809_153201](/assets/sp160809_153201.png) -->

#### Border Width

```CSS
p.one {
    border-style: solid;
    border-width: 5px;
}

p.two {
    border-style: solid;
    border-width: medium;
}

p.three {
    border-style: dotted;
    border-width: 2px;
}

p.four {
    border-style: dotted;
    border-width: thick;
}

p.five {
    border-style: double;
    border-width: 15px;
}

p.six {
    border-style: double;
    border-width: thick;
}

p.seven {
    border-style: solid;
    border-width: 2px 10px 4px 20px;
}
```
![sp160809_153450](http://ooo.0o0.ooo/2016/08/09/57a987cf7f7b2.png)

<!-- ![sp160809_153450](/assets/sp160809_153450.png) -->


#### Border Color

```CSS
p.one {
    border-style: solid;
    border-color: red;
}

p.two {
    border-style: solid;
    border-color: yellow;
}

p.three {
    border-style: solid;
    border-color: red green blue yellow;
}
```
![sp160809_153628](/assets/sp160809_153628_2dyp0ah50.png)

<!-- ![sp160809_153628](/assets/sp160809_153628.png) -->

#### Border - Individual Sides

```CSS
p {
    border-top-style: dotted;
    border-right-style: solid;
    border-bottom-style: dotted;
    border-left-style: solid;
}
```
![sp160809_154252](http://ooo.0o0.ooo/2016/08/09/57a989a81bc74.png)

<!-- ![sp160809_154252](/assets/sp160809_154252.png) -->

#### Border - Shorthand Property

```CSS
p {
    border-left: 6px solid red;
    background-color: lightgrey;
}
```
![sp160809_154408](http://ooo.0o0.ooo/2016/08/09/57a989f2f135e.png)

<!-- ![sp160809_154408](/assets/sp160809_154408.png) -->

#### border-radius(CSS3)

可以指定的属性：

- `border-top-left-radius`
- `border-top-right-radius`
- `border-bottom-right-radius`
- `border-bottom-left-radius`

```CSS
#rcorners4 {
    border-radius: 15px 50px 30px 5px;
    background: #73AD21;
    padding: 20px;
    width: 200px;
    height: 150px;
}

#rcorners5 {
    border-radius: 15px 50px 30px;
    background: #73AD21;
    padding: 20px;
    width: 200px;
    height: 150px;
}

#rcorners6 {
    border-radius: 15px 50px;
    background: #73AD21;
    padding: 20px;
    width: 200px;
    height: 150px;
}
#rcorners9 {
    border-radius: 50%;
    background: #73AD21;
    padding: 20px;
    width: 200px;
    height: 200px;
}
```
![sp160809_154940](http://ooo.0o0.ooo/2016/08/09/57a98b4479d11.png)

![sp160809_155228](http://ooo.0o0.ooo/2016/08/09/57a98beb01ac3.png)

<!-- ![sp160809_154940](/assets/sp160809_154940.png) -->

<!-- ![sp160809_155228](/assets/sp160809_155228.png) -->

```CSS
#rcorners7 {
    border-radius: 50px/15px;
    background: #73AD21;
    padding: 20px;
    width: 200px;
    height: 150px;
}

#rcorners8 {
    border-radius: 15px/50px;
    background: #73AD21;
    padding: 20px;
    width: 200px;
    height: 150px;
}
```
![sp160809_155521](http://ooo.0o0.ooo/2016/08/09/57a98c96d29fb.png)

<!-- ![sp160809_155521](/assets/sp160809_155521.png) -->

----

### Border Images(CSS3)

- `border-image`使用简写设置边框各种属性
- `border-image-source`设置表框路径
- `border-image-slice`设置图片切割边界
- `border-image-width`设置图片宽度
- `border-image-outset`设置图片延伸
- `border-image-repeat`设置图片重复

#### border-image

#### border-image-source

设置边框图片

```CSS
#borderimg {
    border-image-source: url(border.png);
}
```

#### border-image-slice

常用属性:

- `number`边框图片像素
- `%`边框图片百分比
- `fill`显示边框图片的中间部分

```CSS
#borderimg1 {
    border: 10px solid transparent;
    padding: 15px;
    -webkit-border-image: url(border.png) round; /* Safari 3.1-5 */
    -o-border-image: url(border.png) round; /* Opera 11-12.1 */
    border-image: url(border.png) round;
    border-image-slice: 50;
}

#borderimg2 {
    border: 10px solid transparent;
    padding: 15px;
    -webkit-border-image: url(border.png) round; /* Safari 3.1-5 */
    -o-border-image: url(border.png) round; /* Opera 11-12.1 */
    border-image: url(border.png) round;
    border-image-slice: 20%;
}

#borderimg3 {
    border: 10px solid transparent;
    padding: 15px;
    -webkit-border-image: url(border.png) round; /* Safari 3.1-5 */
    -o-border-image: url(border.png) round; /* Opera 11-12.1 */
    border-image: url(border.png) round;
    border-image-slice: 30%;
}
```
![sp160809_162529](http://ooo.0o0.ooo/2016/08/09/57a993a592101.png)

<!-- ![sp160809_162529](/assets/sp160809_162529.png) -->

#### border-image-width

边框大小

```CSS
#borderimg1 {
    border: 10px solid transparent;
    padding: 15px;
    border-image-source: url(border.png);
    border-image-repeat: round;
    border-image-slice: 30;
    border-image-width: 10px;
}

#borderimg2 {
    border: 10px solid transparent;
    padding: 15px;
    border-image-source: url(border.png);
    border-image-repeat: round;
    border-image-slice: 30;
    border-image-width: 20px;
}

#borderimg3 {
    border: 10px solid transparent;
    padding: 15px;
    border-image-source: url(border.png);
    border-image-repeat: round;
    border-image-slice: 30;
    border-image-width: 30px;
}
```
![sp160809_162701](http://ooo.0o0.ooo/2016/08/09/57a994023aeb4.png)

<!-- ![sp160809_162701](/assets/sp160809_162701.png) -->

#### border-image-outset

边框溢出

```CSS
#borderimg {
	border: 15px solid transparent;
	padding: 15px;   
	border-image: url(border.png);
	border-image-slice: 30;
	border-image-repeat: round;
	border-image-outset:10px 5px 15px 10px;
}
```
![sp160809_162829](http://ooo.0o0.ooo/2016/08/09/57a994590ba76.png)

<!-- ![sp160809_162829](/assets/sp160809_162829.png) -->


#### border-image-repeat

边框图片显示样式`repeat`、`round`、`stretch`

- `repeat`平铺边框图片
- `round`平铺边框图片，过大的地方自动收缩。
- `stretch`拉伸边框图片。默认。

```CSS
#borderimg1 {
    border: 15px solid transparent;
    padding: 15px;
    border-image-source: url(border.png);
    border-image-repeat: repeat;
    border-image-slice: 30;
}

#borderimg2 {
    border: 15px solid transparent;
    padding: 15px;
    border-image-source: url(border.png);
    border-image-repeat: round;
    border-image-slice: 30;
}

#borderimg3 {
    border: 15px solid transparent;
    padding: 15px;
    border-image-source: url(border.png);
    border-image-repeat: stretch;
    border-image-slice: 30;
}
```
![sp160809_163423](http://ooo.0o0.ooo/2016/08/09/57a995bb84598.png)

<!-- ![sp160809_163423](/assets/sp160809_163423.png) -->
