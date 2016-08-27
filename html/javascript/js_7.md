<!--toc-->

- [数组排序与布尔值](#数组排序与布尔值)
	- [数组排序](#数组排序)
		- [`sort()`排序](#sort排序)
		- [`reverse()`颠倒数组顺序](#reverse颠倒数组顺序)
		- [数组中数字排序](#数组中数字排序)
			- [找到最大或最小值](#找到最大或最小值)
		- [对象排序](#对象排序)

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


----


### 对象排序


-----
