<!-- toc -->

- [javascript表达式](#javascript表达式)
	- [javascript 基本运算符](#javascript-基本运算符)
	- [javascript 赋值](#javascript-赋值)
	- [javascript字符串](#javascript字符串)
	- [javascript比较与逻辑](#javascript比较与逻辑)
	- [javascript 数据类型](#javascript-数据类型)
	- [javascript 函数](#javascript-函数)
	- [javascript 对象](#javascript-对象)
	- [javascript 事件](#javascript-事件)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# javascript表达式

## javascript 基本运算符

运算符 | 解释
:-- | :-
+   | 加
-   | 减
*   | 乘
/   | 除
%   | 余数
++  | 累加
--  | 累减

需要注意下运算的优先级。

[详细地址](http://www.w3schools.com/js/js_arithmetic.asp)

--------------------------------------------------------------------------------

## javascript 赋值

操作符 | 例子     | 实际操作
:-- | :----- | :--------
=   | x=y    | x = y
+=  | x += y | x = x + y
-=  | x -= y | x= x - y
\*=  | x \*= y | x = x * y
/=  | x /= y | x = x / y
%=  | x %= y | x = x % y

[例子参考](http://www.w3schools.com/js/js_assignment.asp)

--------------------------------------------------------------------------------

## javascript字符串

可以使用`+`来串联字符,还可以使用`+=`

```javascript
var txt1 = "John";
var txt2 = "Doe";
document.getElementById("demo").innerHTML = txt1 + " " + txt2;
```

输出结果为`John Doe`

--------------------------------------------------------------------------------

## javascript比较与逻辑

操作符 | 说明
:-- | :------
==  | 等于
=== | 等于值或类型
!=  | 不等于
!== | 不等于值或类型
\>   | 大于
\<  | 小于
\?  | 三元运算符

--------------------------------------------------------------------------------

## javascript 数据类型

javascript支持的数据类型有：数字，字符，数组，对象等。

```JS
var length = 16;                               // Number
var lastName = "Johnson";                      // String
var cars = ["Saab", "Volvo", "BMW"];           // Array
var x = {firstName:"John", lastName:"Doe"};    // Object
```

----

## javascript 函数

javascript函数使用`function new()`创建函数名。

```JS
<script>
function myFunction(a, b) {
    return a * b;
}
document.getElementById("demo").innerHTML = myFunction(4, 3);
</script>
```

返回值为`12`


----

## javascript 对象

```JS
<script>
var person = {
    firstName: "John",
    lastName : "Doe",
    id       : 5566,
    fullName : function() {
       return this.firstName + " " + this.lastName;
    }
};

document.getElementById("demo").innerHTML = person.fullName();
</script>
```

```JS
<script>
var person = {
    firstName: "John",
    lastName : "Doe",
    id       :  5566
};
document.getElementById("demo").innerHTML =
person["firstName"] + " " + person["lastName"];
</script>
```

上方两种方法返回的值是相同的。

`typeof`值数据类型。

在`javascript`中，当一个变量没有声明时，默认为全局变量。

---

## javascript 事件

```HTML
<button onclick="this.innerHTML=Date()">The time is?</button>
```
效果为点击后显示日期与时间。

被更改的为按钮的文件显示。

还可以使用如下的方法：

```HTML
<button onclick="displayDate()">The time is?</button>

<script>
function displayDate() {
    document.getElementById("demo").innerHTML = Date();
}
</script>
```
下方出现设定时间。

---
