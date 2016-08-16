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
