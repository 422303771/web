<!-- toc -->

- [javascript](#javascript)
	- [为什么学javascript](#为什么学javascript)
	- [`getElementById()`](#getelementbyid)
		- [`getElementById()`控制文字](#getelementbyid控制文字)
		- [`getElementById()`控制图片](#getelementbyid控制图片)
		- [`getElementById()`控制CSS`](#getelementbyid控制css)
		- [`getElementById()`控制元素隐藏`](#getelementbyid控制元素隐藏)
		- [`getElementById()`控制元素显示`](#getelementbyid控制元素显示)
	- [javascript的挂载方式](#javascript的挂载方式)
		- [通过`<script>`标签](#通过script标签)
		- [通过`<script>`的`src`文件](#通过script的src文件)
	- [javascript的显示方法](#javascript的显示方法)
		- [`window.alert()`](#windowalert)
		- [`document.write()`](#documentwrite)
		- [`innerHTML`](#innerhtml)
		- [`console.log()`](#consolelog)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# javascript

## 为什么学javascript

javascript是web开发必学语言之一

1. HTML来定义网页的内容。

2. CSS来指定网页的布局。

3. javascript来编辑网页的行为。

--------------------------------------------------------------------------------

## `getElementById()`

`getElementById()`用来控制指定`id`的用户行为。

### `getElementById()`控制文字

```javascript
document.getElementById("demo").innerHTML = "Hello JavaScript";
```

### `getElementById()`控制图片

```html
<button onclick="document.getElementById('myImage').src='pic_bulbon.gif'">Turn on the light</button>

<img id="myImage" src="pic_bulboff.gif" style="width:100px">

<button onclick="document.getElementById('myImage').src='pic_bulboff.gif'">Turn off the light</button>
```

开启关闭电灯。本质上是，替换两种图片。

![sp160815_151022](http://ooo.0o0.ooo/2016/08/15/57b16b229c699.png)

<!-- ![sp160815_151022](/assets/sp160815_151022.png) -->

 ### `getElementById()`控制CSS`

```html
<p id="demo">JavaScript can change the style of an HTML element.</p>

<button type="button" onclick="document.getElementById('demo').style.fontSize='35px'">Click Me!</button>
```

### `getElementById()`控制元素隐藏`

```html

<p id="demo">JavaScript can hide HTML elements.</p>

<button type="button" onclick="document.getElementById('demo').style.display='none'">Click Me!</button>
```

### `getElementById()`控制元素显示`

```html
<p id="demo" style="display:none">Hello JavaScript!</p>

<button type="button" onclick="document.getElementById('demo').style.display='block'">Click Me!</button>
```

--------------------------------------------------------------------------------

## javascript的挂载方式

### 通过`<script>`标签

```HTML
<script>
document.getElementById("demo").innerHTML = "My First JavaScript";
</script>
```

### 通过`<script>`的`src`文件

```JS
function myFunction() {
   document.getElementById("demo").innerHTML = "Paragraph changed.";
}
```
```HTML
<!DOCTYPE html>
<html>
<body>
<script src="myScript.js"></script>
</body>
</html>
```


---

## javascript的显示方法

### `window.alert()`

弹出警告窗口

![sp160815_163327](http://ooo.0o0.ooo/2016/08/15/57b17e9808402.png)

<!-- ![sp160815_163327](/assets/sp160815_163327.png) -->

### `document.write()`

写入HTML输出

### `innerHTML`

写入一个HTML元素

### `console.log()`

写入浏览器控制台

![sp160815_163251](http://ooo.0o0.ooo/2016/08/15/57b17e77544e0.png)

<!-- ![sp160815_163251](/assets/sp160815_163251.png) -->
