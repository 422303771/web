<!--toc-->

- [数组排序与布尔值](#数组排序与布尔值)
	- [数组排序](#数组排序)
		- [`sort()`排序](#sort排序)
		- [`reverse()`颠倒数组顺序](#reverse颠倒数组顺序)
		- [数组中数字排序](#数组中数字排序)
			- [找到最大或最小值](#找到最大或最小值)
		- [对象排序](#对象排序)
	- [布尔](#布尔)
		- [布尔值](#布尔值)
		- [布尔函数](#布尔函数)
		- [默认情况下`true`与`false`](#默认情况下true与false)

<!-- tocstop -->

----


# 数组排序与布尔值

## 数组排序

### `sort()`排序

`sort()`使用从`A`到`Z`的顺序排序。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;

function myFunction() {
    fruits.sort();
    document.getElementById("demo").innerHTML = fruits;
}
```
返回结果为`Apple,Banana,Mango,Orange`。


----


### `reverse()`颠倒数组顺序

例如数组`["Banana", "Orange", "Apple", "Mango"]`,只使用`reverse()`结果为`Mango,Apple,Orange,Banana`

下方代码为，先使用`sort()`，再使用`reverse()`。结果为`Z`到`A`排序。

```JS
var fruits = ["Banana", "Orange", "Apple", "Mango"];
document.getElementById("demo").innerHTML = fruits;

function myFunction() {
    fruits.sort();
    fruits.reverse();
    document.getElementById("demo").innerHTML = fruits;
}
```

返回的结果为`Orange,Mango,Banana,Apple`。


----

### 数组中数字排序

JS中队数字的排序时，使用`sort()`不能得到正确的结果。

可以通过设定下面的方法解决问题。

```JS
var points = [40, 100, 1, 5, 25, 10];
document.getElementById("demo").innerHTML = points;

function myFunction() {
    points.sort(function(a, b){return a - b});
    document.getElementById("demo").innerHTML = points;
}
```

返回数组为`1,5,10,25,40,100`。

如果想反序排列，使用下方方法。

```JS
var points = [40, 100, 1, 5, 25, 10];
points.sort(function(a, b){return b - a});
```

返回结果为`100,40,25,10,5,1`


-----



#### 找到最大或最小值

找最大值，原理是将数组由大到小排列。随后打印第一个元素。

```JS
var points = [40, 100, 1, 5, 25, 10];
points.sort(function(a, b){return b-a});
document.getElementById("demo").innerHTML = points[0];
// 现在 points[0] 就是最大值了。
```

找最小值，原理是将数组由小到大排列。随后打印第一个元素。

```JS
var points = [40, 100, 1, 5, 25, 10];
points.sort(function(a, b){return a-b});
document.getElementById("demo").innerHTML = points[0];
// 现在 points[0] 就是最小值了。
```


----


### 对象排序

下方例子为排序对象数组

例子一，为按照年限排列数组。

```JS
var cars = [
{type:"Volvo", year:2016},
{type:"Saab", year:2001},
{type:"BMW", year:2010}]

displayCars();

function myFunction() {
    cars.sort(function(a, b){return a.year - b.year});
    displayCars();
}

function displayCars() {
  document.getElementById("demo").innerHTML =
  cars[0].type + " " + cars[0].year + "<br>" +
  cars[1].type + " " + cars[1].year + "<br>" +
  cars[2].type + " " + cars[2].year;
}
```

返回结果如下：

![sp160828_215833](http://ooo.0o0.ooo/2016/08/28/57c2ee14d4764.png)

<!-- ![sp160828_215833](/assets/sp160828_215833.png) -->


例子二，为按照产品名排列数组。

```JS
var cars = [
{type:"Volvo", year:2016},
{type:"Saab", year:2001},
{type:"BMW", year:2010}]

displayCars();

function myFunction() {
    cars.sort(function(a, b){
        var x = a.type.toLowerCase();
        var y = b.type.toLowerCase();
        if (x < y) {return -1;}
        if (x > y) {return 1;}
        return 0;
    });
    displayCars();
}

function displayCars() {
  document.getElementById("demo").innerHTML =
  cars[0].type + " " + cars[0].year + "<br>" +
  cars[1].type + " " + cars[1].year + "<br>" +
  cars[2].type + " " + cars[2].year;
}
```
返回结果为：

![sp160828_220026](http://ooo.0o0.ooo/2016/08/28/57c2ee835c53f.png)

<!-- ![sp160828_220026](/assets/sp160828_220026.png) -->



-----


## 布尔

### 布尔值

布尔只有两个值`true`与`false`。

### 布尔函数

使用`Boolean()`来判断，返回值是`true`还是`false`。

下方是例子:

```JS
function myFunction() {
    document.getElementById("demo").innerHTML = Boolean(10 > 9);
}
```

也可以使用更简单的方法。

```JS
function myFunction() {
    document.getElementById("demo").innerHTML = 10 > 9;
}
```

运算符说明


|运算符|解释|
|:---|:---|
|==|等于|
|>|大于|
|<|小于|

### 默认情况下`true`与`false`

以下是`true`
```
100

3.14

-15

"Hello"

"false"

7 + 1 + 3.14

5 < 6
```

以下是`false`

```JS

//The Boolean value of 0 (zero) is false:
var x0 = 0;
Boolean(x0);       // returns false


//The Boolean value of 0 (zero) is false:
var x1 = -0;
Boolean(x1);       // returns false


//The Boolean value of "" (empty string) is false:
var x2 = "";
Boolean(x2);       // returns false


//The Boolean value of undefined is false:
var x3;
Boolean(x3);       // returns false


//The Boolean value of null is false:
var x4 = null;
Boolean(x4);       // returns false


//The Boolean value of false is (you guessed it) false:
var x5 = false;
Boolean(x5);       // returns false


//The Boolean value of NaN is false:
var x6 = 10 / "H";
Boolean(x6);       // returns false
```
