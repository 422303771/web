<!--toc-->

- [javascript 数字](#javascript-数字)
	- [javascript 数字](#javascript-数字-1)
		- [十进制与小数](#十进制与小数)
		- [十六进制](#十六进制)
		- [无穷](#无穷)
		- [NaN](#nan)
		- [数字可以是一个对象](#数字可以是一个对象)
	- [javascript 数字方法](#javascript-数字方法)
	- [javascript 运算](#javascript-运算)
		- [运算对象](#运算对象)
		- [`Math.min()`与`Math.max()`](#mathmin与mathmax)
		- [`Math.random()`](#mathrandom)
		- [`Math.round()`](#mathround)
		- [`Math.ceil()`](#mathceil)
		- [`Math.floor()`](#mathfloor)

<!-- tocstop -->

----

# javascript 数字

## javascript 数字

### 十进制与小数

javascript的数字可以有小数，也可以没有小数。

```JS
var x = 34.00;    // A number with decimals
var y = 34;       // A number without decimals
```
另外一个表达方式,貌似更实用:

```JS
var x = 123e5;    // 12300000
var y = 123e-5;   // 0.00123
```

javascript中最大正数为15位。

最大小数点后17为，并不精确。

所有进行小数点运算时，最好使用如下方法。

```JS
var x = (0.2 * 10 + 0.1 * 10) / 10;       // x will be 0.3
```

### 十六进制

javascript读取的`16进制`多以`0x`开头。

```JS
var x = 0xFF;             // x will be 255
```
下方为`2进制`、`8进制`、`10进制`、`16进制`。

以`10进制`数字`128`为例子。

```JS
var myNumber = 128;
myNumber.toString(16);     // returns 80
myNumber.toString(8);      // returns 200
myNumber.toString(2);      // returns 10000000
```
### 无穷

无穷大`Infinity`,无穷小`-Infinity`。`Infinity`的类型被作为数字。

```JS
var x =  2 / 0;          // x will be Infinity
var y = -2 / 0;          // y will be -Infinity
```

### NaN

`NaN`是`Not a Number`的缩写。下方结果为`NaN`

```JS
var x = 100 / "Apple";  // x will be NaN (Not a Number)
```

验证结果是否为`NaN`,可以使用自带`isNaN()`函数。

```JS
var x = 100 / "Apple";
isNaN(x);               // returns true because x is Not a Number
```

### 数字可以是一个对象

```JS
var x = 123;
var y = new Number(123);

// typeof x returns number
// typeof y returns object
```

----

## javascript 数字方法


----

## javascript 运算

### 运算对象

### `Math.min()`与`Math.max()`

### `Math.random()`

### `Math.round()`

### `Math.ceil()`

### `Math.floor()`

----
