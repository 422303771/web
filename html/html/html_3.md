<!--toc-->

- [HTML 颜色与CSS](#html-颜色与css)
	- [HTML 默认颜色](#html-默认颜色)
		- [使用RGB值表现颜色](#使用rgb值表现颜色)
		- [使用HEV值表现颜色](#使用hev值表现颜色)
	- [CSS](#css)
		- [行CSS](#行css)
		- [内部CSS](#内部css)
		- [外部CSS](#外部css)
		- [CSS 字体属性](#css-字体属性)
		- [ID与](#id与)

<!-- tocstop -->

----

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

----

## CSS

### 行CSS

在特定标签直接写入`style`

```HTML
<h1 style="color:blue;">This is a Blue Heading</h1>
```

### 内部CSS

在页面头部写入`<style></style>`标签之间。

```HTML
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

```HTML
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

----

###  CSS 字体属性

- `color`字体颜色
- `font-family`字体名称
- `font-size`字体大小
- `border`文字外边框线
- `padding`文字与边框间距离
- `margin`边框与边框间距离

```HTML
p {
    border: 1px solid powderblue;
}
```

:arrow_down:

![sp160807_015840](http://ooo.0o0.ooo/2016/08/06/57a625797b606.png)

<!-- ![sp160807_015840](/assets/sp160807_015840.png) -->

```HTML
p {
    border: 1px solid powderblue;
    padding: 30px;
}
```

:arrow_down:

![sp160807_015914](http://ooo.0o0.ooo/2016/08/06/57a6259947806.png)

<!-- ![sp160807_015914](/assets/sp160807_015914.png) -->

```HTML
p {
    border: 1px solid powderblue;
    margin: 50px;
}
```

:arrow_down:

![sp160807_015939](http://ooo.0o0.ooo/2016/08/06/57a625b6880d2.png)

<!-- ![sp160807_015939](/assets/sp160807_015939.png) -->

----

### ID与class
