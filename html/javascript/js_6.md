<!-- toc -->

- [日期与数组](#日期与数组)
	- [日期](#日期)
		- [转换为字符串](#转换为字符串)
		- [转换为UTC时间字符串](#转换为utc时间字符串)
		- [转化为容易阅读的字符串](#转化为容易阅读的字符串)
	- [日期格式](#日期格式)
	- [日期方法](#日期方法)
		- [`get`获取`Date`数据](#get获取date数据)
		- [`set`设置`Date`方法](#set设置date方法)
		- [`getUTC`获取`UTC`时间](#getutc获取utc时间)
	- [数组](#数组)
		- [创建一个数组](#创建一个数组)
		- [访问数组](#访问数组)
		- [在已有数组中增加元素](#在已有数组中增加元素)
		- [循环显示数组方法](#循环显示数组方法)
		- [创建一个新数组](#创建一个新数组)
		- [如何判断数组](#如何判断数组)
			- [方法一](#方法一)
			- [方法二](#方法二)
			- [方法三](#方法三)
	- [数组方法](#数组方法)
		- [将数组转换为字符串](#将数组转换为字符串)
		- [添加或移除数组元素](#添加或移除数组元素)
			- [移除元素](#移除元素)
			- [添加元素](#添加元素)
			- [`shift()`添加元素](#shift添加元素)
			- [`unshift()`移除元素](#unshift移除元素)
			- [改变元素内容](#改变元素内容)
			- [`deleting`删除元素](#deleting删除元素)
		- [拼接数组，`splice()`方法。](#拼接数组splice方法)
		- [`concat()`链接多个数组为一个](#concat链接多个数组为一个)
		- [`slice()`数组切片](#slice数组切片)
		- [`valueof()`](#valueof)
	- [数组分类](#数组分类)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# 日期与数组

## 日期

使用日期是，要使用`Date()`函数。

`Date()`中可以设定的类别如下。

`dateString`为数据值。

`year`,`month`,`day`,`hours`,`minutes`,`seconds`,`milliseconds`

年，月，日，小时，分钟，秒，毫秒

```javascript
document.getElementById("demo").innerHTML = Date();
```

显示当前时区的时间

下方是例子

```javascript
var d = new Date("October 13, 2014 11:13:00");
document.getElementById("demo").innerHTML = d;
```

```javascript
var d = new Date(86400000);
document.getElementById("demo").innerHTML = d;
```

### 转换为字符串

使用`toString()`函数

```javascript
d = new Date();
document.getElementById("demo").innerHTML = d.toString();
```

### 转换为UTC时间字符串

使用`toUTCString()`函数。

`UTC`指世界标准时间。

```javascript
var d = new Date();
document.getElementById("demo").innerHTML = d.toUTCString();
```

### 转化为容易阅读的字符串

使用`toDateString()`函数。

```javascript
var d = new Date();
document.getElementById("demo").innerHTML = d.toDateString();
```

## 日期格式

样式         | 显示
---------- | ------------------------------
ISO Date   | "2015-03-25"国际标准
Short Date | "03/25/2015" or "2015/03/25"
Long Date  | "Mar 25 2015" or "25 Mar 2015"
Full Date  | "Wednesday March 25 2015"

## 日期方法

### `get`获取`Date`数据

方法                | 效果
----------------- | -------------------------------------------------
getDate()         | Get the day as a number (1-31)
getDay()          | Get the weekday as a number (0-6)
getFullYear()     | Get the four digit year (yyyy)
getHours()        | Get the hour (0-23)
getMilliseconds() | Get the milliseconds (0-999)
getMinutes()      | Get the minutes (0-59)
getMonth()        | Get the month (0-11)
getSeconds()      | Get the seconds (0-59)
getTime()         | Get the time (milliseconds since January 1, 1970)

`getTime()`返回的从1970年1月1日到现在的毫秒数。

下面是例子

```javascript
var d = new Date();
document.getElementById("demo").innerHTML = d.getTime();
```

还可以使用数组

```javascript
var d = new Date();
var days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];
document.getElementById("demo").innerHTML = days[d.getDay()];
```

### `set`设置`Date`方法

方法                | 效果
----------------- | -------------------------------------------------
setDate()         | Set the day as a number (1-31)
setFullYear()     | Set the year (optionally month and day)
setHours()        | Set the hour (0-23)
setMilliseconds() | Set the milliseconds (0-999)
setMinutes()      | Set the minutes (0-59)
setMonth()        | Set the month (0-11)
setSeconds()      | Set the seconds (0-59)
setTime()         | Set the time (milliseconds since January 1, 1970)

下方是例子，效果为当前日期增加50天。

```javascript
var d = new Date();
d.setDate(d.getDate() + 50);
document.getElementById("demo").innerHTML = d;
```

### `getUTC`获取`UTC`时间

方法                   | 效果
-------------------- | -----------------------------------------------------------
getUTCDate()         | Same as getDate(), but returns the UTC date
getUTCDay()          | Same as getDay(), but returns the UTC day
getUTCFullYear()     | Same as getFullYear(), but returns the UTC year
getUTCHours()        | Same as getHours(), but returns the UTC hour
getUTCMilliseconds() | Same as getMilliseconds(), but returns the UTC milliseconds
getUTCMinutes()      | Same as getMinutes(), but returns the UTC minutes
getUTCMonth()        | Same as getMonth(), but returns the UTC month
getUTCSeconds()      | Same as getSeconds(), but returns the UTC seconds

--------------------------------------------------------------------------------

## 数组

数组是一个对象，在`typeof`中返回的为`object`。

最好将数组当做数组，因为它使用`person[0]`读取数据。

而`对象`使用`person.firstName`读取数据。

下方为代码第一个为数组，第二个为对象。

```javascript
var person = ["John", "Doe", 46];
document.getElementById("demo").innerHTML = person[0];
```

```javascript
var person = {firstName:"John", lastName:"Doe", age:46};
document.getElementById("demo").innerHTML = person["firstName"];
```

### 创建一个数组

```javascript
var  cars = ["Saab", "Volvo", "BMW"]; //这个不是数组
var cars = new Array("Saab", "Volvo", "BMW"); //这个是数组
```

### 访问数组

下方代码为访问数组中元素

```javascript
var cars = ["Saab", "Volvo", "BMW"];
document.getElementById("demo").innerHTML = cars[0];
```

下方代码为数组的全部内容

```javascript
var cars = ["Saab", "Volvo", "BMW"];
document.getElementById("demo").innerHTML = cars;
```

_数组的真正优点在于数组的属性与方法_


```JS
var x = cars.length;   //  length属性返回元素个数
var y = cars.sort();   //  sort()方法排序数组
```

### 在已有数组中增加元素

可以使用`push()`或者`length`添加新元素到数组。

还可以直接写定要插入的位置。

使用`push()`方法

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.push("Lemon");                // adds a new element (Lemon) to fruits
```

使用`length`属性

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits[fruits.length] = "Lemon";     // adds a new element (Lemon) to fruits
```

写定要插入的位置,如果原位置上有元素，新元素会替换旧元素。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;

function myFunction() {
    fruits[3] = "Lemon";
    document.getElementById("demo").innerHTML = fruits[3];
}
```

### 循环显示数组方法

使用`for`写一个循环语句。

```HTML
<p id="demo"></p>

<script>
var fruits, text, fLen, i;

fruits = ["Banana", "Orange", "Apple", "Mango"];
fLen = fruits.length;
text = "<ul>";
for (i = 0; i < fLen; i++) {
    text += "<li>" + fruits[i] + "</li>";
}
text += "</ul>";
document.getElementById("demo").innerHTML = text;
</script>
```

语句效果

![sp160825_225954](http://ooo.0o0.ooo/2016/08/25/57bf0804bf614.png)

<!-- ![sp160825_225954](/assets/sp160825_225954.png) -->

在`JS`的数组中不支持索引，想要使用时，可以使用`object`，或者是`JSON`。

数组中只支持元素排序。

```JS
var person = [];
person[0] = "John";
person[1] = "Doe";
person[2] = 46;
document.getElementById("demo").innerHTML =
person[0] + " " + person.length;
```

返回结果为`John 3`。

### 创建一个新数组

避免使用`new Array()`,要使用`[]`创建数组。

`new Array()`在创建数组时，如果只有一个元素，会出错。

```JS
var points = new Array();         // Bad
var points = [];                  // Good

var points = new Array(40, 100, 1, 5, 25, 10); // Bad
var points = [40, 100, 1, 5, 25, 10];          // Good

```

### 如何判断数组

使用`typeof`时，对数组的判定为`object`，那么就可以使用下面的方法。

#### 方法一

使用`Array.isArray()`,这个方法为`ECMAScript 5`增加，对旧版流量器支持不太友好。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = Array.isArray(fruits);
```

结果为`true`。

#### 方法二

创建一个`isArray()`方法函数,注意，这个函数要求有输入

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = isArray(fruits);

function isArray(x) {
    return x.constructor.toString().indexOf("Array") > -1;
}
```
结果为`true`。

#### 方法三

使用`instanceof`运算符。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits instanceof Array;
```
结果为`true`。

_测试时，`object`也会返回`true`,因为在JS中数组被识别为对象。_

----

## 数组方法

### 将数组转换为字符串

使用`toString()`方法将数组转换为字符串。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits.toString();
```

`join()`与`toString()`相似，不过可以指定，元素间的连接符。

```JS
var fruits = ["Banana", "Orange","Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits.join(" * ");
```

返回的结果为`Banana * Orange * Apple * Mango`


### 添加或移除数组元素

#### 移除元素

使用`pop()`方法，将元素移除数组，默认情况为从最后一个元素开始移除。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.pop();              // Removes the last element ("Mango") from fruits
```

#### 添加元素

使用`push()`方法，将元素添加到数组，默认情况为将元素添加到最后。


#### `shift()`添加元素

使用`shift()`添加元素时，元素被填入`[0]`，即最开始的位置。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
fruits.shift();            // Removes the first element "Banana" from fruits
```

#### `unshift()`移除元素

使用`unshift()`移除元素时，元素将从首位被移除。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;

function myFunction() {
    fruits.unshift("Lemon");
    document.getElementById("demo").innerHTML = fruits;
}
```

效果为，每点击一次，首元素移除一个。


#### 改变元素内容

直接将新的元素写入指定的数据位置。当添加到其他位置时，前方数据可能为空位。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;

function myFunction() {
    fruits[0] = "Kiwi";
    document.getElementById("demo").innerHTML = fruits;
}
```

返回的结果为`Banana`被替换为`Kiwi`。


另一种方法，将元素添加到最后位置。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;

function myFunction() {
    fruits[fruits.length] = "Kiwi";
    document.getElementById("demo").innerHTML = fruits;
}
```

返回结果为，`Kiwi`被添加到数组的最后。

#### `deleting`删除元素

使用`deleting`删除元素，会使数组留下空位。


### 拼接数组，`splice()`方法。

使用`splice()`方法。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;
function myFunction() {
    fruits.splice(2, 0, "Lemon", "Kiwi");
    document.getElementById("demo").innerHTML = fruits;
}
```

`2`代表着要插入数组的位置。
`0`代表要替换后面几位的数据。`0`为不替换，当为`1`是`Apple`被`Lemon`与`Kiwi`替换。
`Lemon`与`Kiwi`为要插入的数据。


使用`splice()`删除数组中元素

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;
function myFunction() {
    fruits.splice(0, 1);
    document.getElementById("demo").innerHTML = fruits;
}
```

效果为，从第一个元素开始逐步删除元素。

`0`为要插入的位置，`1`为覆盖的元素，后方没要添加的元素，因此逐渐删除原数组中元素。

### `concat()`链接多个数组为一个

两个数组合并为一个。

```JS
var myGirls = ["Cecilie", "Lone"];
var myBoys = ["Emil", "Tobias", "Linus"];
var myChildren = myGirls.concat(myBoys);

document.getElementById("demo").innerHTML = myChildren;
```

效果为，`myGirls`的数据在前，`myBoys`的数据在后，`myChuldren`为新数组名称。

将多个数组合并为一个

```JS
var arr1 = ["Cecilie", "Lone"];
var arr2 = ["Emil", "Tobias", "Linus"];
var arr3 = ["Robin", "Morgan"];

var myChildren = arr1.concat(arr2, arr3);

document.getElementById("demo").innerHTML = myChildren;
```

新数组`myChildren`的显示顺序为，`arr1`，`arr2`，`arr3`。


### `slice()`数组切片

当`slice()`只有一个参数时，截取包括参数位后的全部数组。

```JS
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"];
var citrus = fruits.slice(1);
document.getElementById("demo").innerHTML = citrus;
```
返回的结果为`Orange,Lemon,Apple,Mango`。

* `slice()`有两个参数时

当`slice()`有两个参数时，如`slice(1,3)`数组为`["Banana", "Orange", "Lemon", "Apple", "Mango"]`。

那么函数的意思为，从`Orange`开始截取，对于第一个参数`1`,即数组第2个元素。

截取到`Lemon`,对应第二个参数`3`,这里的`3`指数组的第3的元素。

_注意：第一个参数从`0`开始记位，第二个参数从`1`开始记位，这里比较混乱。_

```JS
var fruits = ["Banana", "Orange", "Lemon", "Apple", "Mango"];
var citrus = fruits.slice(1,3);
document.getElementById("demo").innerHTML = fruits + "<br>" + citrus;
```

### `valueof()`

将数组返回一个对象。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits.valueOf();
```

`javascript`本身会将数组转换为字符串。

`valueOF()`将数组转换为对象。

`toString()`将数组换位有`,`的字符串。


_全部数组方法见:[Array Methods](http://www.w3schools.com/jsref/jsref_obj_array.asp)。_

----

## 数组分类

--------------------------------------------------------------------------------
