<!--toc-->

- [日期与数组](#日期与数组)
	- [日期](#日期)
		- [转换为字符串](#转换为字符串)
		- [转换为UTC时间字符串](#转换为utc时间字符串)
		- [转化为容易阅读的字符串](#转化为容易阅读的字符串)
	- [日期格式](#日期格式)
	- [日期方法](#日期方法)
		- [`get`获取`Date`数据](#get获取date数据)
		- [`set`设置`Date`](#set设置date)
	- [数组](#数组)
	- [数组方法](#数组方法)
	- [数组分类](#数组分类)

<!-- tocstop -->

---

# 日期与数组

## 日期

使用日期是，要使用`Date()`函数。

`Date()`中可以设定的类别如下。

`dateString`为数据值。

`year`,`month`,`day`,`hours`,`minutes`,`seconds`,`milliseconds`

年，月，日，小时，分钟，秒，毫秒

```JS
document.getElementById("demo").innerHTML = Date();
```

显示当前时区的时间

下方是例子

```js
var d = new Date("October 13, 2014 11:13:00");
document.getElementById("demo").innerHTML = d;
```

```js
var d = new Date(86400000);
document.getElementById("demo").innerHTML = d;
```

### 转换为字符串

使用`toString()`函数

```js
d = new Date();
document.getElementById("demo").innerHTML = d.toString();
```

### 转换为UTC时间字符串

使用`toUTCString()`函数。

`UTC`指世界标准时间。

```JS
var d = new Date();
document.getElementById("demo").innerHTML = d.toUTCString();
```


### 转化为容易阅读的字符串

使用`toDateString()`函数。

```JS
var d = new Date();
document.getElementById("demo").innerHTML = d.toDateString();
```

## 日期格式

| 样式 | 显示 |
|--|--|
| ISO Date | "2015-03-25"国际标准 |
| Short Date | "03/25/2015" or "2015/03/25" |
| Long Date | "Mar 25 2015" or "25 Mar 2015" |
| Full Date | "Wednesday March 25 2015" |

## 日期方法

### `get`获取`Date`数据

| 方法 | 效果 |
|--|--|
| getDate() | Get the day as a number (1-31) |
| getDay() | Get the weekday as a number (0-6) |
| getFullYear() | Get the four digit year (yyyy) |
| getHours() | Get the hour (0-23) |
| getMilliseconds() | Get the milliseconds (0-999) |
| getMinutes() | Get the minutes (0-59) |
| getMonth() | Get the month (0-11) |
| getSeconds() | 	Get the seconds (0-59) |
| getTime() | Get the time (milliseconds since January 1, 1970) |

`getTime() `返回的从1970年1月1日到现在的毫秒数。

下面是例子

```JS
var d = new Date();
document.getElementById("demo").innerHTML = d.getTime();
```

还可以使用数组

```JS
var d = new Date();
var days = ["Sunday","Monday","Tuesday","Wednesday","Thursday","Friday","Saturday"];
document.getElementById("demo").innerHTML = days[d.getDay()];
```

### `set`设置`Date`方法

| 方法 | 效果 |
|--|--|
| setDate() | Set the day as a number (1-31) |
| setFullYear() | Set the year (optionally month and day) |
| setHours() | Set the hour (0-23) |
| setMilliseconds() | Set the milliseconds (0-999) |
| setMinutes() | Set the minutes (0-59) |
| setMonth() | Set the month (0-11) |
| setSeconds() | Set the seconds (0-59) |
| setTime() | Set the time (milliseconds since January 1, 1970) |


----

## 数组

## 数组方法

## 数组分类

-----
