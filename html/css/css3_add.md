<!--toc-->

- [CSS3 Add](#css3-add)
	- [css3 images](#css3-images)
		- [图片圆角化](#图片圆角化)
		- [图片边框](#图片边框)
		- [移入阴影](#移入阴影)
		- [随页面调整大小](#随页面调整大小)
	- [CSS3 按钮](#css3-按钮)
	- [CSS3 翻页](#css3-翻页)

<!-- tocstop -->

---

# CSS3 Add

## css3 images

### 图片圆角化

```CSS
`img {
    border-radius: 50%;
}`
```

![sp160814_111924](http://ooo.0o0.ooo/2016/08/13/57afe37c38422.png)

<!-- ![sp160814_111924](/assets/sp160814_111924.png) -->

### 图片边框

```CSS
img {
    border: 1px solid #ddd;
    border-radius: 4px;
    padding: 5px;
}
```

![sp160814_112125](http://ooo.0o0.ooo/2016/08/13/57afe3f4e08dd.png)

<!-- ![sp160814_112125](/assets/sp160814_112125.png) -->

### 移入阴影

```CSS
a {
    display: inline-block;
    border: 1px solid #ddd;
    border-radius: 4px;
    padding: 5px;
    transition: 0.3s;
}

a:hover {
    box-shadow: 0 0 2px 1px rgba
    (0, 140, 186, 0.5);
}
```

![sp160814_112249](http://ooo.0o0.ooo/2016/08/13/57afe457f11dc.png)

<!-- ![sp160814_112249](/assets/sp160814_112249.png) -->

### 随页面调整大小

```CSS
img {
    max-width: 100%;
    height: auto;
}
```

---

## CSS3 按钮

```CSSS
.button {
    background-color: #4CAF50; /* Green */
    border: none;
    color: white;
    padding: 15px 32px;
    text-align: center;
    text-decoration: none;
    display: inline-block;
    font-size: 16px;
}
```
![sp160815_145024](http://ooo.0o0.ooo/2016/08/15/57b1667600b41.png)

<!-- ![sp160815_145024](/assets/sp160815_145024.png) -->

## CSS3 翻页

```CSS
ul.pagination {
    display: inline-block;
    padding: 0;
    margin: 0;
}

ul.pagination li {
		display: inline;
}

ul.pagination li a {
    color: black;
    float: left;
    padding: 8px 16px;
    text-decoration: none;
    transition: background-color .3s;
    border: 1px solid #ddd;
}

ul.pagination li a.active {
    background-color: #4CAF50;
    color: white;
    border: 1px solid #4CAF50;
}

ul.pagination li a:hover:not(.active) {
		background-color: #ddd;
}

div.center {
	text-align: center;
}

```

![sp160815_145230](http://ooo.0o0.ooo/2016/08/15/57b166ed438ad.png)

<!-- ![sp160815_145230](/assets/sp160815_145230.png) -->
