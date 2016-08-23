<!-- toc -->

- [javascript 数字](#javascript-数字)
	- [javascript 数字](#javascript-数字-1)
		- [十进制与小数](#十进制与小数)
		- [十六进制](#十六进制)
		- [无穷](#无穷)
		- [NaN](#nan)
		- [数字可以是一个对象](#数字可以是一个对象)
	- [javascript 数字方法](#javascript-数字方法)
		- [`toString()`](#tostring)
		- [`toExponential()`](#toexponential)
		- [`toFixed()`](#tofixed)
		- [`toPrecision()`](#toprecision)
		- [`valueOf()`方法](#valueof方法)
		- [关于数字的全局方法](#关于数字的全局方法)
		- [数字的属性](#数字的属性)
	- [javascript 运算](#javascript-运算)
		- [`Math.min()`与`Math.max()`](#mathmin与mathmax)
		- [`Math.random()`](#mathrandom)
		- [`Math.round()`](#mathround)
		- [`Math.ceil()`](#mathceil)
		- [`Math.floor()`](#mathfloor)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# javascript 数字

## javascript 数字

### 十进制与小数

javascript的数字可以有小数，也可以没有小数。

```javascript
var x = 34.00;    // A number with decimals
var y = 34;       // A number without decimals
```

另外一个表达方式,貌似更实用:

```javascript
var x = 123e5;    // 12300000
var y = 123e-5;   // 0.00123
```

javascript中最大正数为15位。

最大小数点后17为，并不精确。

所有进行小数点运算时，最好使用如下方法。

```javascript
var x = (0.2 * 10 + 0.1 * 10) / 10;       // x will be 0.3
```

### 十六进制

javascript读取的`16进制`多以`0x`开头。

```javascript
var x = 0xFF;             // x will be 255
```

下方为`2进制`、`8进制`、`10进制`、`16进制`。

以`10进制`数字`128`为例子。

```javascript
var myNumber = 128;
myNumber.toString(16);     // returns 80
myNumber.toString(8);      // returns 200
myNumber.toString(2);      // returns 10000000
```

### 无穷

无穷大`Infinity`,无穷小`-Infinity`。`Infinity`的类型被作为数字。

```javascript
var x =  2 / 0;          // x will be Infinity
var y = -2 / 0;          // y will be -Infinity
```

### NaN

`NaN`是`Not a Number`的缩写。下方结果为`NaN`

```javascript
var x = 100 / "Apple";  // x will be NaN (Not a Number)
```

验证结果是否为`NaN`,可以使用自带`isNaN()`函数。

```javascript
var x = 100 / "Apple";
isNaN(x);               // returns true because x is Not a Number
```

### 数字可以是一个对象

```javascript
var x = 123;
var y = new Number(123);

// typeof x returns number
// typeof y returns object
```

--------------------------------------------------------------------------------

## javascript 数字方法

### `toString()`

`toString()`返回一个数字作为字符串。

```javascript
var x = 123;
x.toString();            // returns 123 from variable x
(123).toString();        // returns 123 from literal 123
(100 + 23).toString();   // returns 123 from expression 100 + 23
```

### `toExponential()`

`toExponential()` 用来确定小数点后位数。

```javascript
var x = 9.656;
x.toExponential(2);     // returns 9.66e+0
x.toExponential(4);     // returns 9.6560e+0
x.toExponential(6);     // returns 9.656000e+0
```

### `toFixed()`

`toFixed()`返回一个字符串，指定小数点后方位数。

```javascript
var x = 9.656;
x.toFixed(0);           // returns 10
x.toFixed(2);           // returns 9.66
x.toFixed(4);           // returns 9.6560
x.toFixed(6);           // returns 9.656000
```

### `toPrecision()`

`toPrecision()`返回字符串的设置长度。

```javascript
var x = 9.656;
x.toPrecision();        // returns 9.656
x.toPrecision(2);       // returns 9.7
x.toPrecision(4);       // returns 9.656
x.toPrecision(6);       // returns 9.65600
```

### `valueOf()`方法

返回数字。

```javascript
var x = 123;
x.valueOf();            // returns 123 from variable x
(123).valueOf();        // returns 123 from literal 123
(100 + 23).valueOf();   // returns 123 from expression 100 + 23
```

--------------------------------------------------------------------------------

### 关于数字的全局方法

`Number()`方法，只返回数字。

`parseFloat()`方法，可以返回一个浮点数，只返回第一个有效数字。

`parseInt()`方法，返回一个整数，只返回第一个有效数字。

### 数字的属性

`MAX_VALUE`数字的最大值。 `MIN_VALUE`数字的最小值。 `POSITIVE_INFINITY`正无穷。 `NEGATIVE_INFINITY`负无穷。 `NaN`不是数字。

--------------------------------------------------------------------------------

## javascript 运算

### `Math.min()`与`Math.max()`

筛选给定数组的最小值与最大值

### `Math.random()`

返回`0`到`1`直接的随机数

### `Math.round()`

四舍五入

### `Math.ceil()`

有小数时升位。

例如`4.1`使用`Math.ceil()`后，值是`5`

### `Math.floor()`

抹去整数后的小数。

例如`4.9`使用`Math.floor()`后，值是`4`

**其他运算方法，[点这里](http://www.w3schools.com/js/js_math.asp)**

--------------------------------------------------------------------------------
