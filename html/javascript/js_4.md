<!--toc-->

- [javascript 字符串](#javascript-字符串)
	- [javascript 字符串](#javascript-字符串-1)
		- [javascript 字符长度](#javascript-字符长度)
		- [javascript 特殊字符](#javascript-特殊字符)
		- [javascript 创建字符串为对象](#javascript-创建字符串为对象)
	- [javascript 字符串的方法与属性](#javascript-字符串的方法与属性)
		- [`Length`](#length)
		- [`indexOf()`与`lastIndexOf()`](#indexof与lastindexof)
		- [`search()`](#search)
		- [`slice()`](#slice)
		- [`substring()`](#substring)
		- [`substr()`](#substr)
		- [`replace()`](#replace)
		- [`toUpperCase()`与`toLowerCase()`](#touppercase与tolowercase)
		- [`concat()`](#concat)
		- [`charAt()`与`charCodeAt()`](#charat与charcodeat)

<!-- tocstop -->

----

# javascript 字符串

## javascript 字符串

对字符串设定是可以使用`" "`或`' '`。

```JS
var carName1 = "Volvo XC60";
var carName2 = 'Volvo XC60';
```
上方返回的结果是一样的。

也可以在引用中使用`' '`或`" "`只要不冲突。

---

### javascript 字符长度

```JS
<script>
var txt = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
document.getElementById("demo").innerHTML = txt.length;
</script>
```
使用`.length`来判定字符长度。中文同样有效。

上方例子的返回结果为`26`。


----

### javascript 特殊字符

javascript有时或错误的理解代码引用。

```JS
var x = 'It\'s alright';
var y = "We are the so-called \"Vikings\" from the north."
```

其他可以参见下表：

代码 | 输入
:---|:---
\\'|单引号
\\"|双引号
\\\|双斜杠
\\n|新行
\\r|回车
\\t|制表
\\b|退格
\\f|换页

----

### javascript 创建字符串为对象

```JS
<script>
var x = "John";              // x is a string
var y = new String("John");  // y is an object

document.getElementById("demo").innerHTML =
typeof x + "<br>" + typeof y;
</script>
```
返回结果`typeof x`为 `String`, `typeof y`为 `Object`。


验证字符是否相同时：

```JS
<script>
var x = "John";              // x is a string
var y = new String("John");  // y is an object
document.getElementById("demo").innerHTML = (x==y);
</script>
```
结果为`true`。


验证字符类是否相同时:

```JS
<script>
var x = "John";              // x is a string
var y = new String("John");  // y is an object
document.getElementById("demo").innerHTML = (x===y);
</script>
```

结果为`false`。

验证两个对象是否相同:

```JS
<script>
var x = new String("John");  // x is an object
var y = new String("John");  // y is an object
document.getElementById("demo").innerHTML = (x==y);
</script>

// (x == y) is false because x and y are different objects
// (x == x) is true because both are the same object
```

返回值为`false`。

----

## javascript 字符串的方法与属性

### `Length`

返回字符串长度。

```JS
var txt = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
var sln = txt.length;
```

----

### `indexOf()`与`lastIndexOf()`

`indexOf()`查询设定字符位置。从右向左。

```HTML
<p id="p1">Please locate where 'locate' occurs!.</p>

<button onclick="myFunction()">Try it</button>

<p id="demo"></p>

<script>
function myFunction() {
    var str = document.getElementById("p1").innerHTML;
    var pos = str.indexOf("locate");
    document.getElementById("demo").innerHTML = pos;
}
</script>
```
返回的结果为`7`。

`lastIndexOf()`查询设定字符位置。从左向右。

```HTML
<p id="p1">Please locate where 'locate' occurs!.</p>

<button onclick="myFunction()">Try it</button>

<p id="demo"></p>

<script>
function myFunction() {
    var str = document.getElementById("p1").innerHTML;
    var pos = str.lastIndexOf("locate");
    document.getElementById("demo").innerHTML = pos;
}
</script>
```
返回的结果为`21`。

`indexOf()`与`lastIndexOf()`二者的计数都从`0`开始的。

当值为空时,返回结果为`-1`。

---

### `search()`

```HTML

<p id="p1">Please locate where 'locate' occurs!.</p>

<button onclick="myFunction()">Try it</button>

<p id="demo"></p>

<script>
function myFunction() {
    var str = document.getElementById("p1").innerHTML;
    var pos = str.search("locate");
    document.getElementById("demo").innerHTML = pos;
}
</script>
```

搜索字符,结果为`7`。

----

### `slice()`

当`slice()`内的值为`正数`时，从左向右查找。当值为`负数`时。从右向左查找。

```JS
var str = "Apple, Banana, Kiwi";
var res = str.slice(7,13);
```
返回值为`Banana`。

```JS
var str = "Apple, Banana, Kiwi";
var res = str.slice(-12,-6);
```

返回值也为`Banana`。

```JS
var res = str.slice(7);
```
换回值为`Banana, Kiwi`。

```JS
var res = str.slice(-12);
```
返回值为`Banana, Kiwi`。


----

### `substring()`

与`slice()`相似，但不接受`负数`。

----

### `substr()`

与`slice()`相似，但第二个参数为`字符长度`

---

### `replace()`

更换字符串内容。

```HTML
<button onclick="myFunction()">Try it</button>

<p id="demo">Please visit Microsoft!</p>
<script>
function myFunction() {
    var str = document.getElementById("demo").innerHTML;
    var txt = str.replace("Microsoft","W3Schools");
    document.getElementById("demo").innerHTML = txt;
}
</script>
```

点击`button`后，`Microsoft`变为`W3Schools`。

默认情况下`replace()`只替换第一个匹配。要替换所有匹配，使用`g`标识。

```HTML
<button onclick="myFunction()">Try it</button>

<p id="demo">Please visit Microsoft and Microsoft!</p>

<script>
function myFunction() {
    var str = document.getElementById("demo").innerHTML;
    var txt = str.replace(/Microsoft/g,"W3Schools");
    document.getElementById("demo").innerHTML = txt;
}
</script>
```
点击`button`后，全部的`Microsoft`变为`W3Schools`。

`replace()`并不会改变字符内容，它会创建新的字符串。

---

### `toUpperCase()`与`toLowerCase()`

`toUpperCase()`将全部字母变为大写。

```JS
var text1 = "Hello World!";       // String
var text2 = text1.toUpperCase();  // text2 is text1 converted to upper
```
返回值为`HELLO WORLD!`。

`toLowerCase()`将全部字母变为小写。

```JS
var text1 = "Hello World!";       // String
var text2 = text1.toLowerCase();  // text2 is text1 converted to lower
```

返回值为`hello world!`。


----

### `concat()`

`concat()`用于叠加字符。

```JS
var text1 = "Hello";
var text2 = "World";
text3 = text1.concat("	",text2);
```

---

### `charAt()`与`charCodeAt()`

`charAt()`方法，返回一个指定的字符。

```JS
var str = "HELLO WORLD";
str.charAt(0);            // returns H
```

`charCodeAt()`方法，返回一个指定的字符的`Unicode`。

```JS
var str = "HELLO WORLD";

str.charCodeAt(0);         //	returns 72
```

----
