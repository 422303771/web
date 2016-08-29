<!-- toc -->

- [JS 比较运算符、判断语句、循环语句](#js-比较运算符-判断语句-循环语句)
	- [JS 比较运算符](#js-比较运算符)
		- [逻辑运算](#逻辑运算)
		- [条件运算](#条件运算)
		- [不同类型间的比较](#不同类型间的比较)
		- [32-bit 的运算符](#32-bit-的运算符)
	- [JS 判断语句](#js-判断语句)
		- [if 语句](#if-语句)
			- [`if`语句](#if语句)
			- [`if……else`语句](#ifelse语句)
			- [`if……else if`语句](#ifelse-if语句)
		- [switch语句](#switch语句)
	- [JS 循环语句](#js-循环语句)
		- [For循环语句](#for循环语句)
		- [While循环语句](#while循环语句)
	- [JS 循环语句打断与继续](#js-循环语句打断与继续)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# JS 比较运算符、判断语句、循环语句

## JS 比较运算符

下方说明在`x = 5`的情况下进行。

![sp160829_111310](http://ooo.0o0.ooo/2016/08/29/57c418da43c9c.png)

<!-- ![sp160829_111310](/assets/sp160829_111310.png) -->

 ### 逻辑运算

下方例子的默认情况为`x = 6,y = 3`。

![sp160829_192050](http://ooo.0o0.ooo/2016/08/29/57c41a9c054b4.png)

<!-- ![sp160829_192050](/assets/sp160829_192050.png) -->

 ### 条件运算

条件运算使用方法

```javascript
variablename = (condition) ? value1:value2;
```

下方为例子代码。

```javascript
function myFunction() {
    var age, voteable;
    age = document.getElementById("age").value;
    voteable = (age < 18) ? "Too young":"Old enough";
    document.getElementById("demo").innerHTML = voteable + " to vote.";
}
```

当结果小于`18`时，返回`Too young`，当结果大于等于`18`时，返回`Old enough`.

### 不同类型间的比较

默认情况下，JS会将字符转化为数字后，进行比较。空位以`0`替换。

非数字的字符，转换为`NaN`。

![sp160829_232542](http://ooo.0o0.ooo/2016/08/29/57c453fe47901.png)

<!-- ![sp160829_232542](/assets/sp160829_232542.png) -->

 其中的`"2">"12"`是因为在比较中`2`后方的空位比较时，将加`0`。即`20>12`。

### 32-bit 的运算符

![sp160830_002639](http://ooo.0o0.ooo/2016/08/29/57c462450ae3e.png)

<!-- ![sp160830_002639](/assets/sp160830_002639.png) -->

 --------------------------------------------------------------------------------

## JS 判断语句

基本上所有的语言通过的判断语句`if`与`switch`

`if`一般与`else`一起使用。

`switch`与`case`一起使用。

### if 语句

#### `if`语句

```javascript
if (new Date().getHours() < 18) {
    document.getElementById("demo").innerHTML = "Good day!";
}
```

当时间小于18点时，返回`Good day!`,其他情况无输出。

#### `if……else`语句

```javascript
function myFunction() {
    var hour = new Date().getHours();
    var greeting;
    if (hour < 18) {
        greeting = "Good day";
    } else {
        greeting = "Good evening";
    }
    document.getElementById("demo").innerHTML = greeting;
}
```

当时间小于18点时，返回`Good day!`，否则返回`Good evening`。

#### `if……else if`语句

```javascript
function myFunction() {
    var greeting;
    var time = new Date().getHours();
    if (time < 10) {
        greeting = "Good morning";
    } else if (time < 20) {
        greeting = "Good day";
    } else {
        greeting = "Good evening";
    }
document.getElementById("demo").innerHTML = greeting;
}
```

当时间小于10点时，返回`Good morning`；

当时间小于18点时，返回`Good day!`；

否则返回`Good evening`。

### switch语句

--------------------------------------------------------------------------------

## JS 循环语句

### For循环语句

### While循环语句

## JS 循环语句打断与继续
