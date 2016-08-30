<!--toc-->

- [JS 类型转换](#js-类型转换)
	- [JS 基本数据类型](#js-基本数据类型)
		- [查看数据类型](#查看数据类型)
		- [查看函数构造](#查看函数构造)
		- [全局方法`String()`](#全局方法string)
		- [使用全局方法`Number()`](#使用全局方法number)
	- [JS 正则表达式](#js-正则表达式)
		- [使用`search()`搜索](#使用search搜索)
		- [使用`replace()`替换](#使用replace替换)

<!-- tocstop -->

----


# JS 类型转换

## JS 基本数据类型

### 查看数据类型

使用`typeof`方法，可以查看数据的类型。

五种数据类型

* string
* number
* boolean
* object
* function

三种数据对象

* Object
* Date
* Array

两种不能识别提示

* null
* undefined

_下方是一些特殊数据类型_

* NaN  是 number
* array 是 object
* date 是 object
* null 是 object

下方为使用例子:

```JS
typeof "John"                 // Returns "string"
typeof 3.14                   // Returns "number"
typeof NaN                    // Returns "number"
typeof false                  // Returns "boolean"
typeof [1,2,3,4]              // Returns "object"
typeof {name:'John', age:34}  // Returns "object"
typeof new Date()             // Returns "object"
typeof function () {}         // Returns "function"
typeof myCar                  // Returns "undefined" *
typeof null                   // Returns "object"
```

### 查看函数构造

使用`constructor `方法

下方为使用例子:

```JS
"John".constructor                 // Returns "function String()  { [native code] }"
(3.14).constructor                 // Returns "function Number()  { [native code] }"
false.constructor                  // Returns "function Boolean() { [native code] }"
[1,2,3,4].constructor              // Returns "function Array()   { [native code] }"
{name:'John', age:34}.constructor  // Returns" function Object()  { [native code] }"
new Date().constructor             // Returns "function Date()    { [native code] }"
function () {}.constructor         // Returns "function Function(){ [native code] }"
```

### 全局方法`String()`

可以使用`String()`也可以使用`toString()`

* 使用`String()`将`number`转换为`string`。

```JS
String(x)         // returns a string from a number variable x
String(123)       // returns a string from a number literal 123
String(100 + 23)  // returns a string from a number from an expression
```

* 使用`String()`将`boolean`转换为`string`。

```JS
String(false)        // returns "false"
String(true)         // returns "true"
```

* 使用`String()`将`date`转换为`string`

```JS
String(Date())      // returns "Thu Jul 17 2014 15:38:19 GMT+0200 (W. Europe Daylight Time)"
```

### 使用全局方法`Number()`

* 将`String`转换为`number`

```JS
Number("3.14")    // returns 3.14
Number(" ")       // returns 0
Number("")        // returns 0
Number("99 88")   // returns NaN
```

* 将`Boolean`转换为`number`

```JS
Number(false)     // returns 0
Number(true)      // returns 1
```

* 将`Date`转换为`number`

-----

## JS 正则表达式

```JS
var patt = /w3schools/i;
```

代码说明:

`/w3schools/i ` 这是一个正则表达式

`w3schools ` 是一个在搜索中使用的模式

`i` 不区分大小写

### 使用`search()`搜索

`search()`方法使用表达式进行搜索，并返回匹配的位置。

```JS
function myFunction() {
    var str = "Visit W3Schools!";
    var n = str.search(/w3Schools/i);
    document.getElementById("demo").innerHTML = n;
}
```

上方代码的返回结果为`6`

### 使用`replace()`替换

`replace()`方法将匹配到的`string`替换。

```JS
function myFunction() {
    var str = document.getElementById("demo").innerHTML;
    var txt = str.replace(/microsoft/i,"W3Schools");
    document.getElementById("demo").innerHTML = txt;
}
```

----
