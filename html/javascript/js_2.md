<!--toc-->

- [javascript 语法](#javascript-语法)
	- [基本语法](#基本语法)
		- [`var`定义变量](#var定义变量)
		- [支持字符样式](#支持字符样式)
		- [赋值](#赋值)
		- [运算](#运算)
		- [连续字符处理](#连续字符处理)
		- [取出选定值](#取出选定值)
	- [运算与判断](#运算与判断)
		- [基本方法](#基本方法)
		- [数值计算](#数值计算)
		- [句尾分隔](#句尾分隔)
		- [换行](#换行)
		- [代码块](#代码块)
	- [JS注释](#js注释)
	- [段注释](#段注释)
	- [命名原则](#命名原则)

<!-- tocstop -->


----


# javascript 语法

## 基本语法

### `var`定义变量

可以使用如下格式

```JS
var x = 5;
var y = 6;
var z = x + y;
```

----

### 支持字符样式

```JS
10.50
1001

"John Doe"
'John Doe'
```

---

### 赋值

```JS
var x;
x = 6;
```
---

### 运算

```JS
document.getElementById("demo").innerHTML = (5 + 6) * 10;
```
---

### 连续字符处理

```JS
"John" + " " + "Doe"
```

---

### 取出选定值

```JS
var lastName = "Doe";
var lastname = "Peterson";
document.getElementById("demo").innerHTML = lastName;
```

---

## 运算与判断

### 基本方法

在使用`demo`样式处显示`Hello Dolly`

```JS
document.getElementById("demo").innerHTML = "Hello Dolly.";
```

---

### 数值计算

```JS
var x = 5;
var y = 6;
var z = x + y;
document.getElementById("demo").innerHTML = z;
```
输出结果为`11`

### 句尾分隔

使用`;`

```JS
a = 5;
b = 6;
c = a + b;
```
或者这样

```JS
a = 5; b = 6; c = a + b;
```
### 换行

避免单行`js`代码过长。

```js
document.getElementById("demo").innerHTML =
"Hello Dolly.";
```

### 代码块

创建函数，将内容封闭到函数中

```JS
function myFunction() {
    document.getElementById("demo").innerHTML = "Hello Dolly.";
    document.getElementById("myDIV").innerHTML = "How are you?";
}
```

```HTML
<p>
<button type="button" onclick="myFunction()">Try it</button>
</p>
```

---

## JS注释

在`javascript`中使用`//`注释语句,也可以使用`/*`到`*/`进行段注释。

```JS
// Change heading:
document.getElementById("myH").innerHTML = "My First Page";
// Change paragraph:
document.getElementById("myP").innerHTML = "My first paragraph.";
```
或者：

```JS
var x = 5;      // Declare x, give it the value of 5
var y = x + 2;  // Declare y, give it the value of x + 2
```

## 段注释

```JS
/*
The code below will change
the heading with id = "myH"
and the paragraph with id = "myP"
in my web page:
*/
document.getElementById("myH").innerHTML = "My First Page";
document.getElementById("myP").innerHTML = "My first paragraph.";
```

## 命名原则

与其它大多数语言相同

下方就使用数据组：

```HTML
<p id="demo"></p>
<script>
var person = "John Doe", carName = "Volvo", price = 200;
document.getElementById("demo").innerHTML = carName;
</script>
```

javascript下文字关联

```HTML
<p>Add "John" + " " + "Doe":</p>

<p id="demo"></p>

<script>
var x = "John" + " " + "Doe";
document.getElementById("demo").innerHTML = x;
</script>
```
输出显示为`John Doe`。


-----
