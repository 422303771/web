<!-- toc -->

- [HTML 颜色与CSS](#html-颜色与css)
	- [HTML 默认颜色](#html-默认颜色)
		- [使用RGB值表现颜色](#使用rgb值表现颜色)
		- [使用HEV值表现颜色](#使用hev值表现颜色)
	- [CSS](#css)
		- [行CSS](#行css)
		- [内部CSS](#内部css)
		- [外部CSS](#外部css)
		- [CSS 字体属性](#css-字体属性)
		- [ID与class](#id与class)
			- [ID](#id)
			- [class](#class)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# HTML 颜色与CSS

## HTML 默认颜色

HTML中默认的颜色有140种，可以直接使用颜色名。

[详细地址](http://www.w3schools.com/html/html_colors.asp)

![sp160807_003701](http://ooo.0o0.ooo/2016/08/06/57a61258be8e2.png)

<!-- ![sp160807_003701](/assets/sp160807_003701.png) -->

 ### 使用RGB值表现颜色

![sp160807_003835](http://ooo.0o0.ooo/2016/08/06/57a612b31571e.png)

<!-- ![sp160807_003835](/assets/sp160807_003835.png) -->

 ### 使用HEV值表现颜色

![sp160807_003925](http://ooo.0o0.ooo/2016/08/06/57a612e2c8b56.png)

<!-- ![sp160807_003925](/assets/sp160807_003925.png) -->

 --------------------------------------------------------------------------------

## CSS

### 行CSS

在特定标签直接写入`style`

```html
<h1 style="color:blue;">This is a Blue Heading</h1>
```

### 内部CSS

在页面头部写入`<style></style>`标签之间。

```html
<!DOCTYPE html>
<html>
<head>
<style>
body {background-color: powderblue;}
h1   {color: blue;}
p    {color: red;}
</style>
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

![sp160807_013817](http://ooo.0o0.ooo/2016/08/06/57a620b176c04.png)

<!-- ![sp160807_013817](/assets/sp160807_013817.png) -->

 ### 外部CSS

使用外部链接直接引入。

```html
<!DOCTYPE html>
<html>
<head>
  <link rel="stylesheet" href="styles.css">
</head>
<body>

<h1>This is a heading</h1>
<p>This is a paragraph.</p>

</body>
</html>
```

--------------------------------------------------------------------------------

### CSS 字体属性

- `color`字体颜色
- `font-family`字体名称
- `font-size`字体大小
- `border`文字外边框线
- `padding`文字与边框间距离
- `margin`边框与边框间距离

```html
p {
    border: 1px solid powderblue;
}
```

:arrow_down:

![sp160807_015840](http://ooo.0o0.ooo/2016/08/06/57a625797b606.png)

<!-- ![sp160807_015840](/assets/sp160807_015840.png) -->

 --------------------------------------------------------------------------------

```html
p {
    border: 1px solid powderblue;
    padding: 30px;
}
```

:arrow_down:

![sp160807_015914](http://ooo.0o0.ooo/2016/08/06/57a6259947806.png)

<!-- ![sp160807_015914](/assets/sp160807_015914.png) -->

 --------------------------------------------------------------------------------

```html
p {
    border: 1px solid powderblue;
    margin: 50px;
}
```

:arrow_down:

![sp160807_015939](http://ooo.0o0.ooo/2016/08/06/57a625b6880d2.png)

<!-- ![sp160807_015939](/assets/sp160807_015939.png) -->

 --------------------------------------------------------------------------------

### ID与class

#### ID

ID的定义方法。

_注意：一个页面中ID的定义是唯一的。_

```css
#p01 {
    color: blue;
}
```

在HTML中使用

```html
<!DOCTYPE html>
<html>
<head>
<style>
#p01 {
    color: blue;
}
</style>
</head>
<body>

<p>This is a paragraph.</p>
<p>This is a paragraph.</p>
<p id="p01">I am different.</p>

</body>
</html>
```

--------------------------------------------------------------------------------

#### class

class定义方法

```css
p.error {
    color: red;
}
```

在HTML使用方法，class可以多次使用。

```html
<!DOCTYPE html>
<html>
<head>
<style>
p.error {
    color: red;
}
</style>
</head>
<body>

<p>This is a paragraph.</p>
<p>This is a paragraph.</p>
<p class="error">I am different.</p>
<p>This is a paragraph.</p>
<p class="error">I am different too.</p>

</body>
</html>
```

--------------------------------------------------------------------------------
