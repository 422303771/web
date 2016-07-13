---
layout: post

title: jekyll
---

# jekyll

[jekyll官方地址](https://jekyllrb.com/)

[jekyll中文](http://jekyllcn.com/)

## 1. 安装jekyll必要组件

下文是在ubuntu上安装与设置

	$ sudo apt-get install ruby ruby-dev make gcc nodejs

## 1.1 安装jekyll

	$ sudo gem install jekyll

之后查看jekyll版本

	$ jekyll -v

## 1.2 jekyll使用

首先要设置`_config.yml`文件

随后build为site。

	$ jekyll build

build后网站文件在`_site`

使用`jekyll server`运行测试。

	$ jekyll server

浏览器输入`localhost:4000`可以查看网站运行情况。

`_post`文件下位文章列表，可以将文章放入后build。

*注意：文件的头部要使用下方格式*
	
	---
	layout: post
	title: Blogging Like a Hacker
	---
	
## 2. kramdown 语法说明

### 2.1 TOC连接[文章目录]

	* TOC
	{：toc}

生成目录带自动锚点

### 2.2 删除线

	~~删除线~~

### 2.3 数学公式

需要增加`js`用于公式解析

	<script src="https://cdn.mathjax.org/mathjax/latest/MathJax.js?config=TeX-AMS-MML_HTMLorMML" type="text/javascript"></script>

## 3. disqus

添加`disqus`代码到`_layouts/post.html`