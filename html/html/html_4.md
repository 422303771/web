<!-- toc -->

- [HTML Link、图片与表格](#html-link-图片与表格)
	- [:link:Link](#linklink)
		- [link 基本语法](#link-基本语法)
		- [link打开方法](#link打开方法)
		- [图片link](#图片link)
		- [书签](#书签)
	- [图片](#图片)
	- [表格](#表格)

<!-- tocstop -->

 --------------------------------------------------------------------------------

# HTML Link、图片与表格

## :link:Link

### link 基本语法

```html
  <a href="url">link text</a>
```

- 本地连接

  ```html
  <a href="html_images.asp">HTML Images</a>
  ```

- 连接行为

  ```html
  <style>
  a:link {
    color: green;
    background-color: transparent;
    text-decoration: none;
  }
  a:visited {
    color: pink;
    background-color: transparent;
    text-decoration: none;
  }
  a:hover {
    color: red;
    background-color: transparent;
    text-decoration: underline;
  }
  a:active {
    color: yellow;
    background-color: transparent;
    text-decoration: underline;
  }
  </style>
  ```

语法        | 效果
:-------- | :-----------
a:link    | 连接本来样式
a:visited | 连接查看后样式
a:hover   | 鼠标移动到时，连接的样式
a:active  | 鼠标点击时，连接的样式

### link打开方法

```HTML
<a href="http://www.w3schools.com/html/" target="_top">HTML5 tutorial!</a>
```

语法        | 效果
:-------- | :-----------
\_blank  | 在新窗口或标签打开
\_self | 在被点击的窗口中打开，页面不发送跳转。(默认情况)
\_parent  | 在父窗口中跳转
\_top | 在一个全新的窗口中打开，类似弹出窗口。

### 图片link

```HTML
<a href="default.asp">
  <img src="smiley.gif" alt="HTML tutorial" style="width:42px;height:42px;border:0;">
</a>
```
### 书签

```HTML
<a href="#tips">Visit the Useful Tips Section</a>
```

----

## 图片

----

## 表格

---
